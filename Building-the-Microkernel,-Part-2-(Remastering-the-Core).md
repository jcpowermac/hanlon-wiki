Here, we will describe the process of merging together the changes from the work we did in [Building the Microkernel, Part 1 (Creating an Overlay)](Building-the-Microkernel,-Part-1-%28Creating-an-Overlay%29) with the files that we'd like to include in our Microkernel ISO from the Razor-Microkernel project itself (the Razor Microkernel Controller and it's dependencies). The approach taken will be to overlay (unpack) multiple gzipped tarfiles over the top of the filesystem from a 'standard' TCL Core distribution ISO. Each of these tarfiles will add an additional bit of functionality to the underlying ISO, and the resulting directory (containing all of the overlays) will then be used to build a new (Razor Microkernel) ISO that contains all of the functionality that is needed. Here, we will outline the steps taken, the files and scripts involved, and the structure of the resulting 'Razor Microkernel' ISO.