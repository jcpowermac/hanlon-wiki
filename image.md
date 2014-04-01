The Image slice is used to add new images to the Occam system. These images can (currently) be one of three types:

* **mk** -- a Microkernel image; this type of image is used by Occam as a standin for a full operating system image in order to accomplish some task. Currently the only Microkernel images that are defined/used within Occam are 'discovery' Microkernels (which are used to discover node properties and whether or not there is a defined policy that can be mapped to the node), but in the future other Microkernel types might be defined (eg. a Microkernel that performs a boot-nuke or a system audit once booted)
* **os** -- OS images are used by Occam to install an operating system on a node. The source ISO for these images is the same ISO that would be used to install the corresponding OS from an operating system distributor (eg. Redhat, SUSE or Canonical).
* **esxi** -- ESXi images are used to install a VMware ESXi hypervisor onto a node. Like the OS images, the source ISOs for these images are the same ISOs that you would download directly from VMware.

Since there are significant differences between the typical OS image and an ESXi image (especially in terms of the models that are constructed using these images), they are separated out logically here into two different image types. These two image types are meant to be used to construct models (see the model image slice description, below) that can then be used to deploy the corresponding OS (or hypervisor instance) to individual nodes being managed by Occam.

## The image CLI

The image CLI provides users with the ability to add new images to the system, view the details of the images that have been added to the system, and remove images from the system. Note that there is no ability to update an image provided via the image CLI. Here is a high-level summary of the commands that are available via the image CLI:
```bash
occam image [get] [all]         View all images (detailed list)
occam image [get] (UUID)        View details of specified image
occam image add (options...)    Add a new image to the system
occam image remove (UUID)       Remove existing image from the system
```
There are options that are required when adding a new image (of any type) to the system using the 'image add' command. Those options are shown below:
```bash
Usage: occam image add (options...)
   -t, --type TYPE             The type of image (mk, os, or esxi)
   -p, --path PATH             The local path to the image ISO
   -n, --name OS_NAME          The logical name to use (os images only)
   -v, --version OS_VERSION    The version to use (os images only)
```
In this command, the TYPE  that is included in the command must be one of the aforementioned image types (mk, os, or esxi) and the PATH that is included is the (local, perhaps even relative) path to the image ISO being added. The OS_NAME and OS_VERSION are strings that are required, but only when adding an OS image to the system. For the MK and ESXi image types these two parameters are not required (and are silently ignored if they are included as part of the 'occam image add' command).

Note that when it comes to these last two parameters (OS_NAME and OS_VERSION), care should be taken by the user to ensure that these strings correspond to the name and version for the OS instance being added (for example, one could use 'ubuntu-amd64' and '11.10' for these two values when adding a 64-bit Ubuntu Oneiric ISO to the system).

## The image RESTful API

The image slice RESTful API is provided via two resources ('/image' and '/image/{UUID}'), but these endpoints are only accessible from the Occam subnet (and are meant to be used either by the corresponding CLI methods or by nodes that are network booting and are using Occam as their source for boot images). The image slice's RESTful API is not intended to be the primary interface to this functionality (and, as such, is not documented here).
