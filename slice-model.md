# Model Slice

The *model* slice is used to manage OS deployment profiles. Each operating system have a model template consist of two parts. First, the common installation settings such as kernel args, kickstart/autoyast/preseed answer files, and postinstall scripts. Second, a list of OS installation settings that requires user input such as hostname, username, password which will be prompted during the creation of a new model.

## Model Templates

Current razor ships with the following model templates:

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

Once an operatings system ISO is loaded into the image service, we can selet from the list of existing templates on the system for deployment.

When creating a new model, any model template metadata requiring use input will be prompted for the appropriate setting:

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
