Because of the nature of different servers and compatibility with PXE and iPXE you may run into issues with the default PXE/iPXE configuration of Razor.

The examples below are using ISC DHCP.

Most installations of Razor will use the direct PXE loading hook with Razor. This method involves setting the next-server and filename DHCP options to point to your Razor server and the 'pxelinux.0' file respectively.

This process uses the burned-in PXE command code to load Razor's SYSLINUX pxeconfig.0 code which in turn presents a menu. This menu is set to boot to Razor by default but allows the option to bypass Razor completely. If Razor is booted then an iPXE kernel is loaded (ipxe.lkrn) and then hooks into the Razor API for further instructions.

This process does 2 chain loads. One from burned in PXE to Razor's PXELINUX. And then from PXELINUX to iPXE. For some network cards this causes issues.

You can reduce this to 1 chain load by removing the PXE bypass menu and directly loading into Razor.
The example below is an ISC DHCP config uses the default method:

## Default Config

```
subnet 192.168.99.0 netmask 255.255.255.0 {
  range 192.168.99.100 192.168.99.200;
  option subnet-mask 255.255.255.0;
  option broadcast-address 192.168.99.255;
  option routers 192.168.99.10;
  filename "pxelinux.0"; # default loading
  next-server 192.168.99.10;
}
```

And this example is using if/else to bypass the boot menu and directly chain load to iPXE and Razor:

## Direct to iPXE Config
```
subnet 192.168.99.0 netmask 255.255.255.0 {
  range 192.168.99.100 192.168.99.200;
  option subnet-mask 255.255.255.0;
  option broadcast-address 192.168.99.255;
  option routers 192.168.99.10;
  if exists user-class and option user-class = "iPXE" {
      filename "razor.ipxe"; # we are in an iPXE kernel and load static script
  } else {
      filename "undionly.kpxe"; # we are in burned in PXE and load iPXE kernel
  }
  next-server 192.168.99.10;
}
```

This should work to fix most issues. A last resort option may be to burn iPXE kernel directly to your network cards. See the instructions here on the iPXE documentation: http://ipxe.org/howto/romburning