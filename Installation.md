# Overview

Razor is a discovery and provisioning tool for baremetal and virtual servers. Razor requires the appropriate files in tftp service directory, and configuration changes to the DHCP service to establish the appropriate pxeboot process to contact the razor service. This document will describe the installation and configuration process to DHCP and TFTP in addition to the Razor software installation process.

The server boot process consists of:

1. acquire IP address via DHCP service.
2. discover tftp server address via DHCP next-server settings.
3. request pxelinux.0 from tftp service.
4. ipxe load razor.ipxe settings from tftp service.
5. ipxe chain boots image from razor service.

Puppet Labs provides a puppetlabs-razor module to install razor and it's dependencies. This document will describe both the puppet installation process, as well as a manual installation process for users that does not run puppet in their environment.

# Installation

## Puppet
Install Puppet Enterprise or Puppet open source.

* [Puppet Enterprise](http://info.puppetlabs.com/download-pe.html)
* [apt.puppetlabs.com](http://apt.puppetlabs.com)
* [yum.puppetlabs.com](http://yum.puppetlabs.com)

Puppet Enterprise installation documentation: http://docs.puppetlabs.com/pe/2.5/install_basic.html

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

The puppetlabs-razor module will install the TFTP service and all support files listed below. Puppet users can skip ahead to the Razor installation instructions.

The TFTP service requires the following files in the tftp directory:

    .
    ├── ipxe.iso
    ├── ipxe.lkrn
    ├── menu.c32
    ├── pxelinux.0
    ├── pxelinux.cfg
    │   └── default
    ├── razor.ipxe
    └── undionly.kpxe

undionly.kpxe is only required for compatibility with certain network cards, such as Broadcoms. See [alternate preboot doc](https://github.com/puppetlabs/Razor/wiki/Alternate-Pre-boot-Options-for-Compatibility) for additional information.

The ipxe and pxelinux files can be obtained from:

* http://ipxe.org/download
* http://www.syslinux.org/wiki/index.php/Download

The razor ipxe file can be generated via the following command:

    razor config ipxe

## Razor

### Puppet Installation

[puppetlabs-razor module](https://github.com/puppetlabs/puppetlabs-razor) supports Razor installation for the following platforms (Debian and Fedora should also work, but not verified):

* Ubuntu Precise
* RHEL 6
* CentOS 6

The puppet module tool will install puppetlabs-razor module from [forge](forge.puppetlabs.com) and automatically resolve all dependencies:

    $ puppet module install puppetlabs-razor
    Preparing to install into /etc/puppet/modules ...
    Downloading from http://forge.puppetlabs.com ...
    Installing -- do not interrupt ...
    /etc/puppet/modules
    /etc/puppet/modules
    └─┬ puppetlabs-razor (v0.2.0)
      ├─┬ puppetlabs-mongodb (v0.1.0)
      │ └── puppetlabs-apt (v0.0.4)
      ├── puppetlabs-nodejs (v0.2.0)
      ├── puppetlabs-ruby (v0.0.2)
      ├── puppetlabs-stdlib (v2.3.3)
      ├── puppetlabs-tftp (v0.1.1)
      ├── puppetlabs-vcsrepo (v0.0.4)
      └── saz-sudo (v2.0.2)

On the puppet master assign the razor class to the appropriate node, and trigger a puppet agent run afterwards:

    node razor_host {
      class { 'sudo':
        config_file_replace => false,
      }      
      class { 'razor':
        username  => 'razor',
        mk_source => 'https://github.com/downloads/puppetlabs/Razor-Microkernel/rz_mk_prod-image.0.9.0.4.iso',
      }
    }

If Puppet is running in a standalone environment without a Puppet master server, simply apply the tests manifests to install razor:

    puppet apply /etc/puppet/modules/razor/tests/init.pp --verbose

Note: installation issues with the puppet module should be filed in the [puppetlabs-razor project](https://github.com/puppetlabs/puppetlabs-razor/issues).

