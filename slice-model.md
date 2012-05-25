# Model Slice

The `model` slice is used to manage OS deployment profiles. Each operating system has a model template consisting of two parts: 
1. the common installation settings such as kernel args, kickstart/autoyast/preseed answer files, and postinstall scripts and 
2. a list of OS installation settings that require user input such as hostname, username, and password. These settings will be entered at prompts during the creation of a new model.

## Model Templates

Currently, Razor ships with the following model templates:

    $ razor model get template
    
    Model Templates:
    Template Name         Description         
    centos_6        CentOS 6 Model            
    debian_wheezy   Debian Wheezy Model       
    opensuse_12     OpenSuSE Suse 12 Model    
    ubuntu_oneiric  Ubuntu Oneiric Model      
    ubuntu_precise  Ubuntu Precise Model      
    vmware_esxi_5   VMware ESXi 5 Deployment  

## Add Model

Once an operating system's ISO is loaded into the image service, it can be selected from the list of existing templates on the system for deployment.

When creating a new model, Razor will provide a prompt for any model template data that requires user input:

    $ razor model add template=ubuntu_precise label=install_precise image_uuid=274HnNlQF5jvbo0y6U0aok
    --- Building Model (ubuntu_precise):
    Please enter node hostname prefix (will append node number) (example: node)
    default: node
    (QUIT to cancel)
    > ubuntu
    Please enter root password (> 8 characters) (example: P@ssword!)
    default: test1234
    (QUIT to cancel)
    > testEnvPass!
    Model created
    Label =>  install_precise
    Template =>  linux_deploy
    Description =>  Ubuntu Precise Model
    UUID =>  3LCN86Cpx0Te3Of5WbORkQ
    Image UUID =>  274HnNlQF5jvbo0y6U0aokI
