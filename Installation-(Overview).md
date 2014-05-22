Hanlon is a discovery and provisioning tool for baremetal and virtual servers. Hanlon requires the appropriate files in tftp service directory, and configuration changes to the DHCP service to establish the appropriate pxeboot process to contact the hanlon service. This document will describe the installation and configuration process to DHCP and TFTP in addition to the Hanlon software installation process.

The server boot process consists of:

1. acquire IP address via DHCP service.
2. discover tftp server address via DHCP next-server settings.
3. request pxelinux.0 from tftp service.
4. ipxe load hanlon.ipxe settings from tftp service.
5. ipxe chain boots image from the Hanlon server.

Puppet Labs provided a puppetlabs-razor module to install Razor and it's dependencies, but a similar module has not been written yet for Hanlon. Fortunately, Hanlon and it's dependencies are simpler to install than Razor (for example, there is no longer a dependency on Node.js and NPM), so we'll focus this document on the manual installation process.

## Installation

The process for installing Hanlon and it's dependencies manually is fairly simple, and is outlined briefly here. For brevity, we are not discussing the installation and basic configuration of some of the external services used by Hanlon here (namely the DHCP and TFTP servers). For these services, only the changes to the 

### DHCP Service

The DHCP server should be configured such that the boot filename to is set to 'pxelinux.0' and set next-server to the tftp server's address:

    filename "pxelinux.0";
    next-server ${tftp_server_ipaddress};

### TFTP Service

The TFTP service requires the following files in the tftp directory:

    .
    ├── ipxe.iso
    ├── ipxe.lkrn
    ├── menu.c32
    ├── pxelinux.0
    ├── pxelinux.cfg
    │   └── default
    ├── hanlon.ipxe
    └── undionly.kpxe

undionly.kpxe is only required for compatibility with certain network cards (some Broadcom cards, for example) that don't support chain-booting using both a PXE and an iPXE-boot process. See [alternate preboot doc](https://github.com/csc/Hanlon/wiki/Alternate-Pre-boot-Options-for-Compatibility) for additional information.

The ipxe and pxelinux files can be obtained from:

* http://ipxe.org/download
* http://www.syslinux.org/wiki/index.php/Download

The hanlon ipxe file can be generated via the following command (once the Hanlon server has been installed and an instance of this server is running):

    hanlon config ipxe

## Manual Installation

The following Hanlon software dependencies must be installed before Hanlon can be installed and run:

* Ruby (1.8.7 or 1.9.3) and Rubygems must be installed on the Hanlon server. Users can install these packages natively (the following two commands can be used to install them natively under an Debian/Ubuntu or CentOS/RHEL system, respectively):

        apt-get install ruby rubygems
        yum install ruby rubygems

    or the same packages could be installed locally using a ruby management system like 'rbenv' or 'rvm'. for the latter two, there are more than enough resources online to guide the typical user through the ruby/rubygems install process. It should also be noted here that if you are choosing the 'native install' option on a RedHat platform, the  rubygems-update package should also be installed and update_rubygems should be executed to update gem version.
* JRuby (1.7.5 or later) must be installed on the system that will be used to build the Hanlon server. This also means that Java (v6 or later) must be installed on that same system (since it's a dependency for running JRuby). We will leave the installation of JRuby and it's dependencies as an exercise for the reader.
* git must be installed on the system that will be used to build the Hanlon server. The Hanlon server can now be built as a WAR file (and deployed to any servlet container in that form), or it can be run locally through a web server environment that supports Rackup applications built using JRuby::Rack (like the trinidad gem). In either case, Hanlon users will have to be able to clone the Hanlon project from the CSC GitHub repository (which requires access to the git command).
* MongoDB or PostgreSQL need to be available to the Hanlon server. By default, Hanlon uses MongoDB to persist the state of the objects that it manages (nodes, images, models, policies, etc.), but the use of PostgreSQL as an alternative object store is a simple configuration change for Hanlon (even if it does require more setup than MongoDB). There are a number of online guides for setting up and configuring both of these servers. You can find a good example for installing MongoDB on a Debian or Ubuntu Linux server here: http://docs.mongodb.org/manual/tutorial/install-mongodb-on-debian-or-ubuntu-linux/

Once the dependencies have been satisfied, hanlon can be installed from source:

    mkdir /opt/hanlon
    cd /opt/hanlon
    git clone https://github.com/csc/Hanlon.git .
    bundle install
