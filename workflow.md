# Razor Workflow

This document describes the workflows in Razor for provisioning a new operating system.

## Add MicroKernel

When Razor is initially installed there are no images loaded in the system. A microkernel image is require to perform the initial inventory discovery process as system connects to Razor.

* Download a microkernel from the project: https://github.com/puppetlabs/Razor/downloads
* Load microkernel via razor image command:

    $ razor image add mk ./rz_mk_dev-image.0.8.8.0.iso
     
    Attempting to add, please wait...
    New image added successfully
    Images:
    UUID: 1nnkuB5BiH1C93HOO0PTFi
    Type: MicroKernel Image
    ISO Filename: rz_mk_dev-image.0.8.8.0.iso
    Path: /opt/razor/image/mk/1nnkuB5BiH1C93HOO0PTFi
    Status: Valid
    Version: 0.8.9.0
    Build Time: 2012-05-09 13:11:01 -0700

Razor will automatically use the newest MK image available in the image service.

## Provision a New OS.

To provision a new OS, Razor requires the following steps

* Add installation ISO to Razor image service.
* Specify model containing the Operating System model template and settings.
* Optionally, a broker for node handoff after provisioning.
* Policy which specify rules to bind nodes to an active model.
* Available system matching all tag rules specified in policy.

The commands and process for provisioning an Ubuntu server is provided below.

### Importing OS Images

The `razor image` command will provide a list of images available on the Razor system. If the desired image is not present, add it to the Razor system as follows:

    $ razor image add os ../ubuntu-12.04-server-amd64.iso ubuntu_precise 12.04
    
    Attempting to add, please wait...
    New image added successfully
    Images:
    UUID: 274HnNlQF5jvbo0y6U0aok
    Type: OS Install
    ISO Filename: ubuntu-12.04-server-amd64.iso
    Path: /mnt/nfs/Razor/image/os/274HnNlQF5jvbo0y6U0aok
    Status: Valid
    OS Name: ubuntu_precise
    OS Version: 12.04

### Create OS Deployment Model

The `razor model get template` command will provide a list of OS templates available in the system. These templates a builtin deployment models that can be used for installing an operating system:

    $ razor model get template
    
    Model Templates:
    Template Name         Description         
    centos_6        CentOS 6 Model            
    debian_wheezy   Debian Wheezy Model       
    opensuse_12     OpenSuSE Suse 12 Model    
    ubuntu_oneiric  Ubuntu Oneiric Model      
    ubuntu_precise  Ubuntu Precise Model      
    vmware_esxi_5   VMware ESXi 5 Deployment  

Once the suitable template is selected, associate the appropriate OS image for software installation, and provide the necessary data for system configuration:

    $ razor model add template=ubuntu_precise label=install_precise image_uuid=274HnNlQF5jvbo0y6U0aok
    --- Building Model (ubuntu_precise):
    Please enter node hostname prefix (will append node number) (example: node)
    default: node
    (QUIT to cancel)
    > ubuntu
    Please enter root password (> 8 characters) (example: P@ssword!)
    default: test1234
    (QUIT to cancel)
    > test1234
    Model created
    Label =>  install_precise
    Template =>  linux_deploy
    Description =>  Ubuntu Precise Model
    UUID =>  3LCN86Cpx0Te3Of5WbORkQ
    Image UUID =>  274HnNlQF5jvbo0y6U0ao

### Create Policy

With the appropriate OS ISO loaded into the image service and matching OS model created, we can specify a policy to deploy this operating system against all nodes that match a specific policy.

    $ razor policy add template=linux_deploy label=precise model_uuid=3LCN86Cpx0Te3Of5WbORkQ broker_uuid=none tags=memsize_4GiB,vmware_vm enabled=true
    Policy created
    UUID =>  41o1z77j2R4ZsgjD9KTpPe
    Line Number =>  2
    Label =>  precise
    Enabled =>  true
    Template =>  linux_deploy
    Description =>  Policy for deploying a Linux-based operating system.
    Tags =>  [vmware_vm]
    Model Label =>  install_precise
    Broker Target =>  none
    Bound Count =>  0

After the initial policy create we have a single rule which can be enabled/disabled. When multiple rules exists in Razor, the first rule that matches is applied similar to how firewall rules behave:

    $ razor policy
    Policies
    #  Enabled     Label         Tags        Model Label    Count           UUID           
    0  true     precise  [memsize_4GiB,vmware_vm]  install_precise  0      41o1z77j2R4ZsgjD9KTpPe

