The Microkernel used by Razor is actually a variant of the Tiny Core Linux (TCL). The current Microkernel is actually built off of their "Core" release (v4.5.2), which can be found [here](http://distro.ibiblio.org/tinycorelinux/4.x/x86/release/Core-current.iso). In order to run the tools that are needed by the Microkernel (Facter for discovery and MCollective for management), we install a number of TCL Extensions as part of our Microkernel. It is these extensions that differentiate our Microkernel instances from the basic TCL Core release.

## Installation (and Configuration) of the Standard TCL Extensions

Unfortunately, there is currently no simple way to install these extensions automatically. Instead, we rely on a procedure that involves booting a VM using an instance of the "standard" TCL Core release, installing (and configuring) the extensions that we will need from the command line, and then taking a snapshot (as a gzipped tarfile) of key parts of the filesystem from that running TCL instance (we will use this snapshot as later, as an overlay, that will replace key parts of the standard TCL Core ISO in order to produce our own Razor Microkernel).

To build out the overlay we need, first we need to install a set of extensions (ruby, bash, dmidecode, lshw, libssl, and a set of key drivers needed by the Microkernel) that are available through the [TCL Extension Repository](http://distro.ibiblio.org/tinycorelinux/4.x/x86/tcz). For the development image we are building here, we will also install an additional extension (openssh) in order to provide command-line access to the Microkernel during development and testing. In a production image, this additional package will, no doubt, be dropped (in order to prevent unauthorized access to the systems that are booted using the Microkernel). Finally, we will install a few additional packages (RubyGems, Facter, and MCollective) along with a couple of key commands needed by the Microkernel (the lscpu and sfdisk commands).  These last few packages and commands will be installed and configured directly from the command line.

The procedure followed for installation of the 'standard' extensions needed is relatively simple. For each extension, we will use the 'tce-load' command to install that extension. The complete set of commands that need to be run are as follows:

    tc@box:~$ tce-load -wi ruby.tcz
    tc@box:~$ tce-load -wi bash.tcz
    tc@box:~$ tce-load -wi dmidecode.tcz
    tc@box:~$ tce-load -wi lshw.tcz
    tc@box:~$ tce-load -wi scsi-3.0.21-tinycore.tcz
    tc@box:~$ tce-load -wi libssl-0.9.8.tcz
    tc@box:~$ tce-load -wi firmware-bnx2.tcz

It should be noted that the version number in the command used to install the scsi drivers (v3.0.21) will change depending on the version of Tiny Core Linux (or TCL) that is being used to build our Microkernel. As we stated earlier, we are currently using TCL v4.5.2, which has a kernel version of 3.0.21.

The only other components from the TCL Extension Repository that are added to the 'standard' TCL Core release when building the Micrkernel are to the the `lscpu` and `sfdisk` commands. These commands are included in the util-linux.tcz extension, but we don't install the extension using tce-load (like we did with the other extensions, above) because there are a few commands that are also included in this extension that cause issues with how Facter works (specifically with whether or not Facter correctly detects whether or not the Microkernel is running in a virtual environment). Instead we extract these commands manually from the util-linux.tcz package and then install them manually.

To install these two commands we first use a wget command to download the until-linux.tcz file itself from the TCL Extension Repository, [here](http://distro.ibiblio.org/tinycorelinux/4.x/x86/tcz/util-linux.tcz), and then mount that file (using a standard 'mount' command) within the Microkernel. Once the extension is mounted, we simply extract the `lscpu` and `sfdisk` executable files from their location within the extension, and place them into the the appropriate directories in the Micrkernel filesytem. Finally, we create soft-links to these commands from directories that are on the default path in the Microkernel (in the `/usr/local/bin` and `/usr/local/sbin` directories for the `lscpu` and `sfdisk` commands, respectively). Here are the commands that we use to accomplish these steps:

    tc@box:~$ wget http://distro.ibiblio.org/tinycorelinux/4.x/x86/tcz/util-linux.tcz
    tc@box:~$ sudo mkdir -p /tmp/tcloop/util-linux/usr/local/bin
    tc@box:~$ sudo mkdir -p /tmp/tcloop/util-linux/usr/local/sbin
    tc@box:~$ mkdir /tmp/util-linux
    tc@box:~$ sudo mount /tmp/util-linux
    tc@box:~$ sudo cp /tmp/util-linux/usr/local/bin/lscpu /tmp/tcloop/util-linux/usr/local/bin
    tc@box:~$ sudo cp /tmp/util-linux/usr/local/sbin/sfdisk /tmp/tcloop/util-linux/usr/local/sbin
    tc@box:~$ sudo mkdir /usr/local/sbin
    tc@box:~$ sudo ln -s /tmp/tcloop/util-linux/usr/local/bin/lscpu /usr/local/bin
    tc@box:~$ sudo ln -s /tmp/util-linux/usr/local/sbin/sfdisk /usr/local/sbin

