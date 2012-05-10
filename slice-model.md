    $ razor model add ubuntu_oneiric_min ubuntu
    
    Available commands for [Model]:
    [get] [add] [remove] 
    
    [Model] [add_cli_with_image] <-ImageUUIDToBindMissing
    
    Command syntax: razor model add (model_template) (model name) {image uuid}
    Images:
      UUID: nVe9chgnS26gYdmCf4NW  
      Type: OS Install  
      ISO Filename: ubuntu-11.10-server-amd64.iso  
      Path: /mnt/nfs/Razor/image/os/nVe9chgnS26gYdmCf4NW  
      Status: Valid   
      OS Name: ubuntu  
      OS Version: 11.10  
    
    
    

    $ razor model add ubuntu_oneiric_min ubuntu nVe9chgnS26gYdmCf4NW
    
    --- Building Model Config(ubuntu_oneiric_min): 
    
    
    Please enter node hostname prefix (will append node number (example: node}) 
    default: node
    (QUIT to cancel)
     > example.vm
    
    Please enter root password (> 8 characters) (example: P@ssword!}) 
    default: test123
    (QUIT to cancel)
     > test12345
    Model Configs:
       Label: ubuntu  Type: ubuntu_oneiric_min  Description: Ubuntu Oneiric 11.10 Minimal
      Model UUID: 5SL6gy6V1OXzFKWOfgsqxG  Image UUID: nVe9chgnS26gYdmCf4NW
    
    $ razor model 
    Models
     Label =>  ubuntu
     Template =>  linux_deploy
     Description =>  Ubuntu Oneiric 11.10 Minimal
     UUID =>  5SL6gy6V1OXzFKWOfgsqxG
    
    
    
