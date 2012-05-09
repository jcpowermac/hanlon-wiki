# Image Slice

The *image* slice is used to list, add, delete images to the image service.

## List Images

List image is the default action for the image slice:

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


## Add Images

Add image requires a valid image type. Current razor supports mk, esxi, and os images:

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
    
    Currently images are availalbe at:https://github.com/lynxbat/Razor/downloads
    
    To add a new microkernel, download the image and install it:

If multiple microkernels are loaded, razor will automatically use the latest MK kernel avaliable.

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

Images by default will be loaded in the image_svc_path:

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

Do not modify files in the image_svc_path.

## Remove Image

Images that are no longer required can be removed by specifying the image UUID:

    $ razor image remove 0115f790628c012f1c14000c29a694fa
    
    Image remove success
    
    Image: 0115f790628c012f1c14000c29a694fa removed successfully

