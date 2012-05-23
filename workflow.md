# Razor Workflow

This document describes common workflows for using Razor to:

* Specify policy to provision a new OS.
* Specify policy to provision an ESXi instance.

## Provision a New OS.

To provision a new OS, Razor uses the following resources:

* OS ISO loaded into Razor image service.
* Suitable target nodes that do not have an active model bound to the system.
* Appropriate tag rules/matches to identify and classify target nodes.
* A model specifying the ISO and completed model template for deployment.
* Optionally, a broker to handoff the node after the Razor active model executes.
* A policy to bind nodes with specific tags to an active model.

The general commands and an Ubuntu provisioning example are shown below:

### Importing OS Images

The `razor image` command will provide a list of images available on the Razor system. If the desired image is not present, add it to the Razor system as follows:

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

Razor will maintain a record of all nodes checked into the system. Nodes with an active policy typically will not actively check-in with Razor. They can be rebound to other policies, but this section will focus on assigning nodes that do not yet have an active policy:

    $ razor node
    Discovered Nodes
        UUID      Last Checkin                          Tags
    000C291BD3B1  42256.6 min   [cpus_2,IntelCorporation,memsize_1,nics_1,vmware_vm]
    000C29C952BA  20s           [cpus_1,IntelCorporation,memsize_1,nics_1,physical]
    000C29AD19C3  14s           [cpus_2,IntelCorporation,memsize_1,nics_1,vmware_vm]
    000C292F835C  45s           [cpus_1,IntelCorporation,memsize_1,nics_1,vmware_vm]
