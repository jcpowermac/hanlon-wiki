# Overview

Occam is a discovery and provisioning tool for baremetal and virtual servers. Occam requires the appropriate files in tftp service directory, and configuration changes to the DHCP service to establish the appropriate pxeboot process to contact the occam service. This document will describe the installation and configuration process to DHCP and TFTP in addition to the Occam software installation process.

The server boot process consists of:

1. acquire IP address via DHCP service.
2. discover tftp server address via DHCP next-server settings.
3. request pxelinux.0 from tftp service.
4. ipxe load occam.ipxe settings from tftp service.
5. ipxe chain boots image from occam service.

Puppet Labs provides a puppetlabs-occam module to install occam and it's dependencies. This document will describe both the puppet installation process, as well as a manual installation process for users that does not run puppet in their environment.

# Installation

## Puppet
Install Puppet Enterprise or Puppet open source.

* [Puppet Enterprise](http://info.puppetlabs.com/download-pe.html)
* [apt.puppetlabs.com](http://apt.puppetlabs.com)
* [yum.puppetlabs.com](http://yum.puppetlabs.com)

Puppet Enterprise installation documentation: http://links.puppetlabs.com/enterpriseinstall

Open Source Puppet installation example script: https://gist.github.com/3170626

## DHCP Service

DHCP configuration should configure the boot filename to 'pxelinux.0' and set next-server to the tftp server's address:

    filename "pxelinux.0";
    next-server ${tftp_server_ipaddress};

[PuppetLabs DHCP Module](https://github.com/puppetlabs/puppetlabs-dhcp) will install and configure dhcp service with the following example manifests:

    class { 'dhcp':
      dnsdomain   => [
                       'puppetlabs.lan',
                       '1.0.10.in-addr.arpa',
                     ],
      nameservers => ['8.8.8.8'],
      ntpservers  => ['us.pool.ntp.org'],
      interfaces  => ['eth0'],
      pxefilename => 'pxelinux.0', # DHCP filename
      pxeserver   => '10.0.1.50',  # DHCP next-server
    }

## TFTP Service

The puppetlabs-occam module will install the TFTP service and all support files listed below. Puppet users can skip ahead to the Occam installation instructions.

The TFTP service requires the following files in the tftp directory:

    .
    ├── ipxe.iso
    ├── ipxe.lkrn
    ├── menu.c32
    ├── pxelinux.0
    ├── pxelinux.cfg
    │   └── default
    ├── occam.ipxe
    └── undionly.kpxe

undionly.kpxe is only required for compatibility with certain network cards, such as Broadcoms. See [alternate preboot doc](https://github.com/puppetlabs/Occam/wiki/Alternate-Pre-boot-Options-for-Compatibility) for additional information.

The ipxe and pxelinux files can be obtained from:

* http://ipxe.org/download
* http://www.syslinux.org/wiki/index.php/Download

The occam ipxe file can be generated via the following command:

    occam config ipxe

### Manual Installation

Manual installation is documented below for environments that does not use puppet:

Occam software dependencies:

* Ruby (1.8.7 or 1.9.3) and Rubygems

        apt-get install ruby rubygems
        yum install ruby rubygems

    On RedHat platforms rubygems-update should be installed and update_rubygems should be executed to update gem version.
* git and make
* MongoDB installation: http://docs.mongodb.org/manual/tutorial/install-mongodb-on-debian-or-ubuntu-linux/
* NodeJS installation: https://github.com/joyent/node/wiki/Installation
* NPM installation: http://npmjs.org/

Once the dependencies have been satisfied, occam can be installed from source:

    mkdir /opt/occam
    cd /opt/occam
    gem install autotest base62 bson bson_ext colored daemons json logger macaddr mocha mongo net-ssh require_all syntax uuid
    npm install express@2.5.11
    npm install mime
    git clone https://github.com/puppetlabs/Occam.git
    /opt/occam/bin/occam_daemon.rb start