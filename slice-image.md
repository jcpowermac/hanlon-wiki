# Image Slice

The *image* slice is used to list, add, and delete .iso images on the image service.

## List Images

The default action for the image slice is list:

    $ razor image
    Images:
      UUID: 0115f790628c012f1c14000c29a694fa
      Type: MicroKernel Image
      ISO Filename: rz_mk_dev-image.0.3.1.0.iso
      Path: /mnt/nfs/Razor/image/mk/0115f790628c012f1c14000c29a694fa
      Status: ^[[AValid
      Version: 0.3.1.0
      Build Time: 2012-03-20 23:13:09 -0700
    
      UUID: f10af5e063da012f1d7a000c29a694fa
      Type: MicroKernel Image
      ISO Filename: rz_mk_dev-image.0.6.4.0.iso
      Path: /mnt/nfs/Razor/image/mk/f10af5e063da012f1d7a000c29a694fa
      Status: Valid
      Version: 0.6.4.0
      Build Time: 2012-04-20 17:45:53 -0700

## Adding MK Images

Adding an image requires a valid image type. Currently Razor supports mk, esxi, and os images:

    $ razor image add
    
    Please select a valid image type.
    Valid types are:
      [esxi] - VMware Hypervisor Install
      [os] - OS Install
      [mk] - MicroKernel Image
    
    Available commands for [Image]:
    [add] [get] [remove] [path]
    
    [Image] [add] <-InvalidImageType
    
    Command syntax: image add [esxi|os|mk] (PATH TO ISO)
    
    Currently images are available at:https://github.com/lynxbat/Razor/downloads
    
    To add a new microkernel, download the image and install it:

Razor will automatically detect the MK version and build time. If multiple microkernels are loaded, Razor will automatically use the latest MK kernel avaliable.

    $ razor image add mk ../rz_mk_dev-image.0.7.2.iso
    
    razor image add mk ../rz_mk_dev-image.0.7.2.0.iso
    Attempting to add, please wait...
    
    New image added successfully
    
    Images:
      UUID: 5dH0ZpO91rBQRVWSvGgpq6
      Type: MicroKernel Image
      ISO Filename: rz_mk_dev-image.0.7.2.0.iso
      Path: /mnt/nfs/Razor/image/mk/5dH0ZpO91rBQRVWSvGgpq6
      Status: Valid
      Version: 0.7.2.0
      Build Time: 2012-05-02 13:47:47 -0700

## Adding OS Images

Razor can load any operating system's installation packages via ISO images.

    $ razor image add os ../ubuntu-11.10-server-amd64.iso 
    
    Available commands for [Image]:
    [add] [get] [remove] [path] 
    
    [Image] [add] <-MissingOSName
    
    Command syntax: image add os ../ubuntu-11.10-server-amd64.iso (OS Name) (OS Version)

Razor currently does not detect information regarding the OS ISO. Consequently, in addition to the path to ISO images, users must also supply an OS name and OS version as follows:

    $ razor image add os ../ubuntu-11.10-server-amd64.iso ubuntu 11.10
    Attempting to add, please wait...
    
    New image added successfully
    
    Images:
      UUID: nVe9chgnS26gYdmCf4NW  
      Type: OS Install  
      ISO Filename: ubuntu-11.10-server-amd64.iso  
      Path: /mnt/nfs/Razor/image/os/nVe9chgnS26gYdmCf4NW  
      Status: Valid   
      OS Name: ubuntu  
      OS Version: 11.10  

## Adding ESXi Images

As with MK images, Razor only requires the image's path and will automatically determine the version of ESXi installation packages.

## Remove Image

Images that are no longer required can be removed by specifying the image's UUID:

    $ razor image remove 0115f790628c012f1c14000c29a694fa
    
    Image remove success
    
    Image: 0115f790628c012f1c14000c29a694fa removed successfully

## Image Storage

Images will be stored in image_svc_path by image type (mk, os, esxi), and image uuid:

    $ tree image
    image
    ├── esxi
    ├── mk
    │   ├── 0115f790628c012f1c14000c29a694fa
    │   │   ├── boot
    │   │   │   ├── core.gz
    │   │   │   ├── isolinux
    │   │   │   │   ├── boot.cat
    │   │   │   │   ├── boot.msg
    │   │   │   │   ├── f2
    │   │   │   │   ├── f3
    │   │   │   │   ├── f4
    │   │   │   │   ├── isolinux.bin
    │   │   │   │   └── isolinux.cfg
    │   │   │   └── vmlinuz
    │   │   └── iso-metadata.yaml
    │   ├── 5dH0ZpO91rBQRVWSvGgpq6
    │   │   ├── boot
    │   │   │   ├── core.gz
    │   │   │   ├── isolinux
    │   │   │   │   ├── boot.cat
    │   │   │   │   ├── boot.msg
    │   │   │   │   ├── f2
    │   │   │   │   ├── f3
    │   │   │   │   ├── f4
    │   │   │   │   ├── isolinux.bin
    │   │   │   │   └── isolinux.cfg
    │   │   │   └── vmlinuz
    │   │   └── iso-metadata.yaml
    │   └── f10af5e063da012f1d7a000c29a694fa
    │       ├── boot
    │       │   ├── core.gz
    │       │   ├── isolinux
    │       │   │   ├── boot.cat
    │       │   │   ├── boot.msg
    │       │   │   ├── f2
    │       │   │   ├── f3
    │       │   │   ├── f4
    │       │   │   ├── isolinux.bin
    │       │   │   └── isolinux.cfg
    │       │   └── vmlinuz
    │       └── iso-metadata.yaml
    └── README

Do not modify files in the image_svc_path, manual changes will result in broken images:

    UUID: nVe9chgnS26gYdmCf4NW
    Type: OS Install
    ISO Filename: ubuntu-11.10-server-amd64.iso
    Path: /mnt/nfs/Razor/image/os/nVe9chgnS26gYdmCf4NW
    Status: Broken/Missing
    OS Name: ubuntu
    OS Version: 11.10

