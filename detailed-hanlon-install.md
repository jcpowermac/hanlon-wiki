# Detailed Hanlon Install Guide

This guide is for individuals just want to get Hanlon installed and running quickly. We are using Ubuntu LTS 14.04 but should also work with previous releases.
 
### Package Prerequisites 
```bash
apt-get install -y git make mongodb openjdk-7-jre-headless g++ isc-dhcp-server ipxe tftp tftpd curl
``` 
### TFTP server configuration and reload
```bash
mkdir /tftpboot
chmod -R 777 /tftpboot
chown -R nobody /tftpboot
```
```bash
cat > /etc/xinetd.d/tftp << EOF
service tftp
{
protocol = udp
port = 69
socket_type = dgram
wait = yes
user = nobody
server = /usr/sbin/in.tftpd
server_args = /tftpboot
disable = no
}
EOF
```
```bash
service xinetd reload
```


### Two Options for iPXE Setup
* Use Ubuntu iPXE, this is ok if you are not installing ESXi.
Copy ipxe files to tftpboot
```bash
cp /usr/lib/ipxe/* /tftpboot
```
* If you have a requirement to install ESXi you will need to build iPXE with different configuration options, see https://github.com/csc/Hanlon/issues/46 for more details.
#### Clone iPXE Git repository 
```bash
git clone git://git.ipxe.org/ipxe.git
```
#### Create Patch File for general.h
```bash
cat > image_comboot.patch << EOF
diff -rupN org_ipxe/src/config/general.h ipxe/src/config/general.h
--- org_ipxe/src/config/general.h 2014-06-11 08:07:39.193081793 -0400
+++ ipxe/src/config/general.h 2014-06-10 15:15:30.628188405 -0400
@@ -111,7 +111,7 @@ FILE_LICENCE ( GPL2_OR_LATER );
//#define IMAGE_PXE /* PXE image support */
//#define IMAGE_SCRIPT /* iPXE script image support */
//#define IMAGE_BZIMAGE /* Linux bzImage image support */
-//#define IMAGE_COMBOOT /* SYSLINUX COMBOOT image support */
+#define IMAGE_COMBOOT /* SYSLINUX COMBOOT image support */
//#define IMAGE_EFI /* EFI image support */
//#define IMAGE_SDI /* SDI image support */
//#define IMAGE_PNM /* PNM image support */
EOF
```
#### Patch general.h
```bash
patch -p0 < image_comboot.patch
```

### Configure PXELINUX

#### Download syslinux
```bash
wget https://www.kernel.org/pub/linux/utils/boot/syslinux/syslinux-6.02.tar.gz
```
#### Extract pxelinux.0 and menu.c32
```bash 
tar -zxvf syslinux-6.02.tar.gz --strip-components 3 -C /tftpboot syslinux-6.02/bios/core/pxelinux.0
tar -zxvf syslinux-6.02.tar.gz --strip-components 4 -C /tftpboot syslinux-6.02/bios/com32/menu/menu.c32
```
#### Create pxelinux configuration
```bash 
mkdir /tftpboot/pxelinux.cfg
 
cat > /tftpboot/pxelinux.cfg/default <<EOF
default menu.c32
prompt 0
menu title Hanlon Boot Menu
 
timeout 50
f1 help.txt
f2 version.txt
 
label hanlon-boot
menu label Automatic hanlon Node Boot
kernel ipxe.lkrn
append initrd=hanlon.ipxe
 
label boot-else
menu label Bypass hanlon
localboot 1
EOF
```
 
### DHCPd Configuration
 
```bash
cat > /etc/dhcp/dhcpd.conf << EOF
ddns-update-style none;
default-lease-time 600;
max-lease-time 7200;
log-facility local7;
 
authorative;
 
option space ipxe;
option ipxe-encap-opts code 175 = encapsulate ipxe;
option ipxe.priority code 1 = signed integer 8;
option ipxe.keep-san code 8 = unsigned integer 8;
option ipxe.skip-san-boot code 9 = unsigned integer 8;
option ipxe.syslogs code 85 = string;
option ipxe.cert code 91 = string;
option ipxe.privkey code 92 = string;
option ipxe.crosscert code 93 = string;
option ipxe.no-pxedhcp code 176 = unsigned integer 8;
option ipxe.bus-id code 177 = string;
option ipxe.bios-drive code 189 = unsigned integer 8;
option ipxe.username code 190 = string;
option ipxe.password code 191 = string;
option ipxe.reverse-username code 192 = string;
option ipxe.reverse-password code 193 = string;
option ipxe.version code 235 = string;
option iscsi-initiator-iqn code 203 = string;
 
option ipxe.pxeext code 16 = unsigned integer 8;
option ipxe.iscsi code 17 = unsigned integer 8;
option ipxe.aoe code 18 = unsigned integer 8;
option ipxe.http code 19 = unsigned integer 8;
option ipxe.https code 20 = unsigned integer 8;
option ipxe.tftp code 21 = unsigned integer 8;
option ipxe.ftp code 22 = unsigned integer 8;
option ipxe.dns code 23 = unsigned integer 8;
option ipxe.bzimage code 24 = unsigned integer 8;
option ipxe.multiboot code 25 = unsigned integer 8;
option ipxe.slam code 26 = unsigned integer 8;
option ipxe.srp code 27 = unsigned integer 8;
option ipxe.nbi code 32 = unsigned integer 8;
option ipxe.pxe code 33 = unsigned integer 8;
option ipxe.elf code 34 = unsigned integer 8;
option ipxe.comboot code 35 = unsigned integer 8;
option ipxe.efi code 36 = unsigned integer 8;
option ipxe.fcoe code 37 = unsigned integer 8;
option ipxe.vlan code 38 = unsigned integer 8;
option ipxe.menu code 39 = unsigned integer 8;
option ipxe.sdi code 40 = unsigned integer 8;
option ipxe.nfs code 41 = unsigned integer 8;
 
option ipxe.no-pxedhcp 1;
 
option hanlon_server code 224 = ip-address;
option hanlon_port code 225 = unsigned integer 16;
option hanlon_base_uri code 226 = text;
 
 
subnet 192.168.66.0 netmask 255.255.255.0 {
range 192.168.66.200 192.168.66.210;
option domain-name "virtomation.com";
option routers 192.168.66.1;
next-server 192.168.66.254;
option tftp-server-name "192.168.66.254";
option domain-name-servers 192.168.66.1;
default-lease-time 600;
max-lease-time 7200;
 
option hanlon_server 192.168.66.254;
option hanlon_port 8026;
option hanlon_base_uri "/hanlon/api/v1";
 
if exists user-class and option user-class = "iPXE" {
filename "hanlon.ipxe";
} else {
filename "undionly.kpxe";
}
 
}
EOF
``` 
### JRuby via RVM
```bash
\curl -sSL https://get.rvm.io | bash -s stable --ruby=jruby
source /usr/local/rvm/scripts/rvm
```
#### Install bundle and trinidad gems
```bash
gem install bundler trinidad
```
 
### Install and configure Hanlon

```bash
mkdir /opt/hanlon
cd /opt/hanlon
```
#### Clone Hanlon git repository
```bash
git clone https://github.com/csc/Hanlon.git .
```
#### Install the prerequisite gems
```bash
bundle install
```
 
#### Run hanlon_init and modify the configuration files
```bash
./hanlon_init
vi /opt/hanlon/web/conf/hanlon_server.conf
vi /opt/hanlon/cli/conf/hanlon_client.conf
```
 
#### Execute run-tri.sh script
```bash
cd /opt/hanlon/script
./run-tri.sh
```

#### Install and configure Ruby 1.9.3 for Hanlon Cli 

```bash
source /usr/local/rvm/scripts/rvm
cd /opt/hanlon
rvm install 1.9.3
rvm use ruby-1.9.3
```
#### Install the prerequisite gems
```bash
bundle install
```

#### Create Hanlon iPXE Configuration
```bash
./cli/hanlon config ipxe > /tftpboot/hanlon.ipxe
```

#### Download Hanlon Microkernel 
 
```bash
wget https://github.com/csc/Hanlon-Microkernel/releases/download/v1.0/hnl_mk_prod-image.1.0.iso
```
 
#### Add the microkernel to the image repository

**_Ignore native BSON extension warning do not install bson_ext._** 
 
```bash
./cli/hanlon image add -t mk -p hnl_mk_prod-image.1.0.iso
** Notice: The native BSON extension was not loaded. **
 
For optimal performance, use of the BSON extension is recommended.
 
To enable the extension make sure ENV['BSON_EXT_DISABLED'] is not set
and run the following command:
 
gem install bson_ext
 
If you continue to receive this message after installing, make sure that
the bson_ext gem is in your load path.
 
Attempting to add, please wait...
Image Added:
UUID => j2B1BswDUNPvGDQ1NXVaM
Type => MicroKernel Image
ISO Filename => hnl_mk_prod-image.1.0.iso
Path => /opt/hanlon/image/mk/j2B1BswDUNPvGDQ1NXVaM
Status => Valid
Version => 1.0
Built Time => 2014-05-21 21:37:12 -0400
 
```
### CentOS Install
 
#### Download the CentOS ISO
```bash
wget http://reflector.westga.edu/repos/CentOS/6.5/isos/x86_64/CentOS-6.5-x86_64-minimal.iso
```
#### Add to the image repository
```bash
./cli/hanlon image add -t os -p CentOS-6.5-x86_64-minimal.iso -n CentOS65 -v 6.5
 
Attempting to add, please wait...
Image Added:
UUID => K3v2suiIkSYNEdx1CTKqs
Type => OS Install
ISO Filename => CentOS-6.5-x86_64-minimal.iso
Path => /opt/hanlon/image/os/K3v2suiIkSYNEdx1CTKqs
Status => Valid
OS Name => CentOS65
OS Version => 6.5
```
#### Create the model for a CentOS deployment
```bash
./cli/hanlon model add -t centos_6 -l centos65-model -i K3v2suiIkSYNEdx1CTKqs
 
Model Created:
Label => centos65-model
Template => linux_deploy
Description => CentOS 6 Model
UUID => 2J5Vb2ztkks9pz1iLrCxks
Image UUID => K3v2suiIkSYNEdx1CTKqs
```
 
#### Create the policy 
```bash
./cli/hanlon policy add --template=linux_deploy -l centos65-policy -m 2J5Vb2ztkks9pz1iLrCxks -t memsize_1GiB --enabled true
```
