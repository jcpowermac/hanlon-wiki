# Using Razor to Provision ESXi 

### Load the VMware-VMvisor-Installer-5.0.0-469512.x86_64.iso into Razor image service.
    $ razor image add [os|mk|esxi] (PATH TO ISO)

    $ razor image add esxi VMware-VMvisor-Installer-5.0.0-469512.x86_64.iso
    Attempting to add, please wait...

    New image added successfully

    Images:
            UUID: 4fhYStNwgcjUMKHvFIChSy  
            Type: VMware Hypervisor Install  
            ISO Filename: VMware-VMvisor-Installer-5.0.0-469512.x86_64.iso  
            Path: /root/macshared/Razor/image/esxi/4fhYStNwgcjUMKHvFIChSy  
            Status: Valid   
            Version: VMware ESXi 5.0 GA

### Create a new model using the esxi template and associate it with the image.

When asked, use the example AAAAA-BBBBB-CCCCC-DDDDD-EEEEE as the ESX License Key.

    $ bin/razor model get template
    Valid Model Templates:
            ubuntu_oneiric_min  :  Ubuntu Oneiric 11.10 Minimal
            vmware_esxi5_simple  :  VMware ESXi 5 Simple Deployment

    $ razor model add (model_template) (model name) {image_uuid}

    $ razor model add vmware_esxi5_simple esxi_model 4fhYStNwgcjUMKHvFIChSy

    --- Building Model Config(vmware_esxi5_simple): 


    Please enter ESX License Key (example: AAAAA-BBBBB-CCCCC-DDDDD-EEEEE}) 
    (QUIT to cancel)
     > AAAAA-BBBBB-CCCCC-DDDDD-EEEEE
    Model Configs:
       Label: esxi_model  Type: vmware_esxi5_simple  Description: VMware ESXi 5 Simple Deployment
      Model UUID: 3FzmV4X8l53CWHu3iZC0DQ  Image UUID: 4fhYStNwgcjUMKHvFIChSy

### Use the `vmware_hypervisor` template to create a policy that connects tags with the model.

    $ razor policy get template

    Policy Templates:
        Template                                                  Description                                              
    vmware_hypervisor  Policy for deploying a VMware hypervisor. Compatible with VMware hypervisor Model Configs           
    linux_deploy       Policy for deploying a Linux-based operating system. Compatible with Linux operating system Models  

    $ razor policy add (policy templates) (Name) (Model Config UUID) [none|(Broker Target UUID)] (tag{,tag,tag})

    $ razor policy add vmware_hypervisor esxi_policy 3FzmV4X8l53CWHu3iZC0DQ none vmware_vm
     UUID =>  2znLG5mHt6cIsh4nVzGcfQ
     Line Number =>  2
     Label =>  esxi_policy
     Template =>  vmware_hypervisor
     Description =>  Policy for deploying a VMware hypervisor. Compatible with VMware hypervisor Model Configs
     Tags =>  [vmware_vm]
     Model Label =>  esxi
     Broker Target =>  none
     Bound Count =>  0

