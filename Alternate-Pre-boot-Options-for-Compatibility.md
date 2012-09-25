Because of the nature of different servers and differences in their support for both PXE and iPXE you may run into issues with the default PXE/iPXE configuration of Razor. The examples shown below make use of the configuration options available in the ISC DHCP server.  For other DHCP servers, the actual configuration options that should be used may vary.

## Optional Configuration - Skip PXE Menu

Most installations of Razor will use the direct PXE loading hook with Razor. This method involves setting the next-server and filename DHCP options to point to your Razor server and the 'pxelinux.0' file respectively. This process uses the burned-in PXE command code to load Razor's SYSLINUX pxeconfig.0 code which in turn presents a VGA-style menu. This menu is set to boot to Razor by default but allows the option to bypass Razor completely. If Razor is used to boot the node, then an iPXE kernel is loaded (ipxe.lkrn) and that iPXE kernel hooks into the Razor API for further instructions.

This process does 2 chain loads: the first from burned in PXE to Razor's PXELINUX and the second from PXELINUX to iPXE. For some network cards this causes issues. You can reduce this to 1 chain load by removing the PXE bypass menu and directly loading into Razor. In order to skip the PXE bypass menu, you will need to obtain the undionly.kpxe file, which can be downloaded [directly from ipxe.org](http://boot.ipxe.org/undionly.kpxe). You will then place this file in the standard TFTP folder on your Razor server (/var/lib/tftpboot for an Ubuntu instance) and modify your DHCP server configuration to use this (undionly.kpxe) image. To highlight the differences between these two configurations, both are shown below.  First, this is the ISC DHCP config that uses the default (pxelinux.0) kernel:

```
subnet 192.168.99.0 netmask 255.255.255.0 {
  range 192.168.99.100 192.168.99.200;
  option subnet-mask 255.255.255.0;
  option broadcast-address 192.168.99.255;
  option routers 192.168.99.10;
  filename "pxelinux.0"; # default loading
  next-server 192.168.99.10;
}
```

While this configuration uses an if/else to bypass the VGA-style default boot menu and directly chain load to iPXE and Razor:

```
subnet 192.168.99.0 netmask 255.255.255.0 {
  range 192.168.99.100 192.168.99.200;
  option subnet-mask 255.255.255.0;
  option broadcast-address 192.168.99.255;
  option routers 192.168.99.10;
  if exists user-class and option user-class = "iPXE" {
      filename "razor.ipxe"; # we are in an iPXE kernel and load static script
  } else {
      filename "undionly.kpxe"; # we are in burned in PXE and load iPXE kernel
  }
  next-server 192.168.99.10;
}
```

The second configuration (or something like it that is appropriate for your DHCP server's configuration) should be used in cases where multiple chain loading issues are a problem (as is the case for some Broadcom cards).

## Optional Configuration - Support for Older Cards

A last resort option may be to burn iPXE kernel directly to your network cards. See the instructions here on the iPXE documentation: http://ipxe.org/howto/romburning