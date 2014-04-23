# Hanlon Workflow

This page describes typical workflows in Hanlon for provisioning a new operating system.

## Add MicroKernel

When Hanlon is initially installed there are no images loaded in the system. A microkernel image is required to perform the initial inventory discovery process as a system connects to Hanlon.

* Download a microkernel (MK) from the project site: https://github.com/puppetlabs/Hanlon/downloads
* Load the microkernel via the `hanlon image` command:

        $ hanlon image add mk ./hnl_mk_dev-image.0.8.8.0.iso
         
        Attempting to add, please wait...
        New image added successfully
        Images:
        UUID: 1nnkuB5BiH1C93HOO0PTFi
        Type: MicroKernel Image
        ISO Filename: hnl_mk_dev-image.0.8.8.0.iso
        Path: /opt/hanlon/image/mk/1nnkuB5BiH1C93HOO0PTFi
        Status: Valid
        Version: 0.8.9.0
        Build Time: 2012-05-09 13:11:01 -0700

Hanlon will automatically use the newest MK image available in the image service.

## Provision a New OS.

To provision a new OS, Hanlon requires the following:

* The installation ISO is available Hanlon image service.
* The model containing the Operating System model template and settings is specified.
* Optionally, a broker for node handoff after provisioning is specified.
* A Policy which specifies rules to bind nodes to an active model is selected.
* A system matching all tag rules as specified in policy is available.

The commands and process for taking these steps to provision an Ubuntu server are shown below.

### Importing OS Images

The `hanlon image` command will provide a list of images available on the Hanlon system. If the desired image is not present, it can be added to the Hanlon system as follows:

    $ hanlon image add os ../ubuntu-12.04-server-amd64.iso ubuntu_precise 12.04
    
    Attempting to add, please wait...
    New image added successfully
    Images:
    UUID: 274HnNlQF5jvbo0y6U0aok
    Type: OS Install
    ISO Filename: ubuntu-12.04-server-amd64.iso
    Path: /mnt/nfs/Hanlon/image/os/274HnNlQF5jvbo0y6U0aok
    Status: Valid
    OS Name: ubuntu_precise
    OS Version: 12.04

### Create OS Deployment Model

The `hanlon model get template` command will provide a list of the OS templates available in the system. These templates provide built-in deployment models that can be used for installing an operating system:

    $ hanlon model get template
    
    Model Templates:
    Template Name         Description         
    centos_6        CentOS 6 Model            
    debian_wheezy   Debian Wheezy Model       
    opensuse_12     OpenSuSE Suse 12 Model    
    ubuntu_oneiric  Ubuntu Oneiric Model      
    ubuntu_precise  Ubuntu Precise Model      
    vmware_esxi_5   VMware ESXi 5 Deployment  

Once the desired template is available, associate the appropriate OS image for software installation, and provide the necessary data for system configuration:

    $ hanlon model add --template=ubuntu_precise --label=install_precise --image_uuid=274HnNlQF5jvbo0y6U0aok
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

With the appropriate OS ISO loaded into the image service and a matching OS model created, a policy can be created to deploy this operating system to all nodes that match the policy.

    $ hanlon policy add --template=linux_deploy --label=precise --model_uuid=3LCN86Cpx0Te3Of5WbORkQ --broker_uuid=none --tags=memsize_4GiB,vmware_vm --enabled
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

The initial policy creates a single rule which can be enabled/disabled. When multiple rules exists in Hanlon, the first rule that matches is applied in a manner similar to how firewall rules behave:

    $ hanlon policy
    Policies
    #  Enabled     Label         Tags        Model Label    Count           UUID           
    0  true     precise  [memsize_4GiB,vmware_vm]  install_precise  0      41o1z77j2R4ZsgjD9KTpPe

