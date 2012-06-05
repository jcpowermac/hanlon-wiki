Razor is an advanced provisioning application which can deploy both bare-metal and virtual systems. It's aimed at solving the problem of how to bring new metal into a state where your existing DevOps/configuration management workflows can take it over.

Newly added machines in a Razor deployment will PXE-boot from a special microkernel image, then check in, provide Razor with inventory information, and wait for further instructions. Razor will consult user-created policy rules to choose which preconfigured model to apply to a new node, which will begin to follow the model's directions, giving feedback to Razor as it completes various steps. Models can include a handoff to a configuration management system or to another system capable of controlling the node (such as a vCenter server taking possession of ESX systems).

Using Razor
-----

Razor's capabilities are divided into "Slices" -- sub-applications which can be used from the command line or from a RESTful API.

See here for [[Razor Slices Command Line Usage]].

See below for a list of slices and their individual usage:

* [[slice bmc]]
* [[slice broker]]
* [[slice config]]
* [[slice image]]
* [[slice log]]
* [[slice model]]
* [[slice node]]
* [[slice policy]]

Alternative configuration and support
-----

* [[Alternate Pre boot Options for Compatibility]]

API Documentation

----

* [[Razor API Overview]]

And see here for a general overview of Razor's [[workflow]]