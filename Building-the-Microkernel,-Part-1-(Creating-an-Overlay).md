The Microkernel used by Razor is actually a variant of the Tiny Core Linux (TCL). The current Microkernel is actually built off of their "Core" release (v4.5.2), which can be found [here](http://distro.ibiblio.org/tinycorelinux/4.x/x86/release/Core-current.iso). In order to run the tools that are needed by the Microkernel (Facter for discovery and MCollective for management), we install a number of TCL Extensions as part of our Microkernel. It is these extensions that differentiate our Microkernel instances from the basic TCL Core release.

## Installation (and Configuration) of the Standard TCL Extensions

Unfortunately, there is currently no simple way to install these extensions automatically. Instead, we rely on a procedure that involves booting a VM using an instance of the "standard" TCL Core release, installing (and configuring) the extensions that we will need from the command line, and then taking a snapshot (as a gzipped tarfile) of key parts of the filesystem from that running TCL instance (we will use this snapshot as later, as an overlay, that will replace key parts of the standard TCL Core ISO in order to produce our own Razor Microkernel).

To build out the overlay we need, first we need to install a set of extensions (ruby, bash, dmidecode, lshw, libssl, and a set of key drivers needed by the Microkernel) that are available through the [TCL Extension Repository](http://distro.ibiblio.org/tinycorelinux/4.x/x86/tcz). For the development image we are building here, we will also install an additional extension (openssh) in order to provide command-line access to the Microkernel during development and testing. In a production image, this additional package will, no doubt, be dropped (in order to prevent unauthorized access to the systems that are booted using the Microkernel). Finally, we will install a few additional packages (RubyGems, Facter, and MCollective) along with a couple of key commands needed by the Microkernel (the lscpu and sfdisk commands). These last few packages and commands will be installed and configured directly from the command line.

The procedure followed for installation of the 'standard' extensions needed is relatively simple. For each extension, we will use the 'tce-load' command to install that extension. The complete set of commands that need to be run are as follows:

    tce-load -wi ruby.tcz
    tce-load -wi bash.tcz
    tce-load -wi dmidecode.tcz
    tce-load -wi lshw.tcz
    tce-load -wi scsi-3.0.21-tinycore.tcz
    tce-load -wi libssl-0.9.8.tcz
    tce-load -wi firmware-bnx2.tcz

It should be noted that the version number in the command used to install the scsi drivers (v3.0.21) will change depending on the version of Tiny Core Linux (or TCL) that is being used to build our Microkernel. As we stated earlier, we are currently using TCL v4.5.2, which has a kernel version of 3.0.21.

The only other components from the TCL Extension Repository that are added to the 'standard' TCL Core release when building the Micrkernel are to the the `lscpu` and `sfdisk` commands. These commands are included in the util-linux.tcz extension, but we don't install the extension using tce-load (like we did with the other extensions, above) because there are a few commands that are also included in this extension that cause issues with how Facter works (specifically with whether or not Facter correctly detects whether or not the Microkernel is running in a virtual environment). Instead we extract these commands manually from the util-linux.tcz package and then install them manually.

To install these two commands we first use a wget command to download the until-linux.tcz file itself from the TCL Extension Repository, [here](http://distro.ibiblio.org/tinycorelinux/4.x/x86/tcz/util-linux.tcz), and then mount that file (using a standard 'mount' command) within the Microkernel. Once the extension is mounted, we simply extract the `lscpu` and `sfdisk` executable files from their location within the extension, and place them into the the appropriate directories in the Micrkernel filesytem. Finally, we create soft-links to these commands from directories that are on the default path in the Microkernel (in the `/usr/local/bin` and `/usr/local/sbin` directories for the `lscpu` and `sfdisk` commands, respectively). Here are the commands that we use to accomplish these steps:

    wget http://distro.ibiblio.org/tinycorelinux/4.x/x86/tcz/util-linux.tcz
    sudo mkdir -p /tmp/tcloop/util-linux/usr/local/bin
    sudo mkdir -p /tmp/tcloop/util-linux/usr/local/sbin
    mkdir /tmp/util-linux
    sudo mount /tmp/util-linux
    sudo cp /tmp/util-linux/usr/local/bin/lscpu /tmp/tcloop/util-linux/usr/local/bin
    sudo cp /tmp/util-linux/usr/local/sbin/sfdisk /tmp/tcloop/util-linux/usr/local/sbin
    sudo mkdir /usr/local/sbin
    sudo ln -s /tmp/tcloop/util-linux/usr/local/bin/lscpu /usr/local/bin
    sudo ln -s /tmp/util-linux/usr/local/sbin/sfdisk /usr/local/sbin

Once all of these 'standard' extensions are installed, we are ready to move on to the next part of the process (installing Facter, MCollective, and their dependencies).

## Installation and Configuration of the MCollective Agent

Installation of the MCollective Agent is actually a bit more involved than the installation of ruby and/or bash was (above). This is mostly because the distribution of MCollective (as Ruby source code) doesn't to include the same sort of "installer" script that is often seen in other Ruby "source code" distributions (like the installation of RubyGems, shown below). As a result, we need to do a bit more work (manually) with MCollective when it comes to setup and configuration. In spite of this fact, the installation of MCollective isn't actually all that difficult. The process that we followed is shown (step-by-step) here:

1. First, we downloaded the source-code distribution from the PuppetLabs website. This link takes you to the main "download" page for MCollective. At the time of this writing, the latest stable version of the MCollective package was v1.2.1. A direct link to that gzipped tarfile is given here. We used a "wget" command to download this file directly to the Microkernel instance from the command line:
    
        wget http://downloads.puppetlabs.com/mcollective/mcollective-1.2.1.tgz
    
1. Once the gzipped tarfile was downloaded to the Microkernel instance, that file was unpacked directly into the "standard" directory that is used for extensions that have been installed under Tiny Core Linux (the /usr/local/tce.installed directory). To do this, we ran a set of commands that look something like the following:
    
        cd /usr/local/tce.installed
        sudo tar zxvf ~tc/mcollective-1.2.1.tgz
    
1. With the mcollective-1.2.1.tgz unpacked to a "standard" location, we created a soft-link to the that directory in order to make our paths a bit simpler. We called that soft-link /usr/local/mcollective. For those with minimal UNIX experience, the command to do this is as follows:
    
        ln -s /usr/local/tce.installed/mcollective-1.2.1 /usr/local/mcollective
    
1. While we are at it, we also created a soft-link to /usr/local/mcollective/mcollectived.rb script in the /usr/local/sbin directory (we called this soft-link /usr/local/sbin/mcollectived). As was the case with the /usr/local/mcollective directory link (above), this can be done with a single command that looks like the following:
    
        ln -s /usr/local/mcollective/mcollectived.rb /usr/local/sbin/mcollectived
    
1. We also needed a set of configuration files for our MCollective Agent. The current implementation of the Microkernel actually uses a fixed configuration for all Microkernel isntances, so for this step of the procedure we simply created a /usr/local/etc/mcollective directory in our MCollective instance, then created a server.cfg file in that directory that looks something like the following:
    
        topicprefix = /topic/
        main_collective = mcollective
        collectives = mcollective
        libdir = /usr/local/mcollective/plugins
        logfile = /var/log/mcollective.log
        loglevel = info
        daemonize = 1
        
        # Plugins
        securityprovider = psk
        plugin.psk = klot2oj2ked2tayn3hu5on7l
        connector = stomp
        
        plugin.stomp.host = ubuntu-server
        plugin.stomp.port = 6163
        plugin.stomp.user = mcollective
        plugin.stomp.password = iwillchangethispassword
        
        # Facts
        factsource = yaml
        plugin.yaml = /usr/local/etc/mcollective/facts.yaml
    
With these changes made, the Microkernel has a working version of the MCollective that can be started and run by our Microkernel Controller. There are additional dependencies that need to be installed (namely RubyGems and the `stomp` gem) before the MCollective daemon can be started on our Microkernel, but we will take care of those shortly.

## Installing RubyGems

RubyGems, available [here](http://rubygems.org/), is used to install a number of 'ruby gems' dynamically each time that the Microkernel boots (currently, the daemons, json_pure, stomp, and facter gems are installed during the final stages of the boot process). These gems are downloaded from a local 'gem repository', but to make all of this possible, the RubyGems framework itself must be installed locally on our Microkernel instance (and available during the boot process). The procedure for installing this package is quite simple, and is outlined here:

1. First we download the RubyGems package from the RubyGems.org site. The current version of the Microkernel uses RubyGems version 1.8.15.  As was the case with the MCollective distribution, a simple 'wget' command can be used to download the gzipped tarfile to the Microkernel instance we are building. The command necessary looks something like this:
    
        wget http://production.cf.rubygems.org/rubygems/rubygems-1.8.15.tgz
    
1. Once the gzipped tarfile is donwloaded, we simply unpack the tarfile and change the current working directory to be the directory that is created when the tarfile is unpacked:
    
        tar zxvf rubygems-1.8.15.tgz
        cd rubygems-1.8.15
    
1. and then install RubyGems using a single command from that directory:
    
        sudo ruby setup.rb
    
With RubyGems installed, we are almost done configuring our new Microkernel instance. We will complete the installation and configuration of the extensions and dependencies that we need to add by installing (and configuring) the OpenSSH extension (below), although this step is actually left as an optional last step (rather than including it in the list of 'standard' extensions that were installed previously) because this last step may or may not be performed (the OpenSSH extension is probably only needed in development systems).