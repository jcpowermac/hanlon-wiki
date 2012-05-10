# Razor Workflow

This document describes a common workflow for using Razor to:

* Specify policy to provision new OS.
* Specify policy to provision ESXi.

## Provision new OS.

To provision a new OS, Razor requires the following resource to be avaiable:

* OS ISO loaded into Razor image service.
* Suitable target nodes that does not have an active model bound to the system.
* Appropriate tag rules/matcher to identify and classify target nodes.
* A model specifying the ISO and completed model template for deployment.
* Optionally specify a broker to handoff node after razor active model execution.
* A policy to bind nodes with specific tags to an active model.

The document will describe the general commands, and provide an Ubuntu provision example.

### Importing OS Images

The razor image command will provide a list of images available on the Razor system. If the image is not present, add it to the razor system via the following commands:

    $ razor image add {iso_image_path} {os_name} {os_version}
    
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

### Select Nodes

Razor will maintain a record of all nodes checked into the system. Nodes with an active policy typically will not actively checkin with razor. They can be rebound to other policies, but this section will focus on assigning nodes that does not yet have an active policy:

    $ razor node
    Discovered Nodes
        UUID      Last Checkin                          Tags
    000C291BD3B1  42256.6 min   [cpus_2,IntelCorporation,memsize_1,nics_1,vmware_vm]
    000C29C952BA  20s           [cpus_1,IntelCorporation,memsize_1,nics_1,physical]
    000C29AD19C3  14s           [cpus_2,IntelCorporation,memsize_1,nics_1,vmware_vm]
    000C292F835C  45s           [cpus_1,IntelCorporation,memsize_1,nics_1,vmware_vm]

In the list of nodes 
