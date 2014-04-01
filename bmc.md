As was mentioned previously, the bmc slice provides Occam with the ability to interact with a node's  Baseboard Management Controller (or BMC), if one exists (not all nodes will have this hardware component, and virtual nodes certainly won't include one).  If the node has a BMC, that BMC can be used to control the power state of the node and to gather additional meta-data about the node, even if the node is not currently powered on.

## The bmc CLI

The bmc CLI provides users with the ability to change a node's power state and to query that node for additional metadata.  In addition, commands are provided that give the user the ability to register a new BMC with the Occam server and to get a list of the current BMCs that are registered with the Occam server (along with the current power-state of the nodes that they are associated with and the serial number of their motherboard, both of which are available through the node's BMC).  The CLI usage for the bmc slice is as follows:
```bash
occam bmc [get] [all]                             Display list of BMCs
occam bmc [get] (UUID) [--query,-q (IPMI_QUERY)]  Display info for a BMC
occam bmc register (options...)                   Registers a new BMC
occam bmc update (UUID) --power-state,-p (STATE)  Change BMC Power State
```
It should be noted here that the IPMI_QUERY value (passed via the '--query' command-line flag) must be one of the following values:

* **info** -- returns information about the BMC itself
* **guid** -- returns the BMC's Globally Unique IDentifier (or GUID)
* **enables** -- returns a list of the features enabled for a specific BMC
* **fru_print** -- displays list of information about the Field Replaceable Units (or FRUs) contained in the node's chassis
* **lan_print** -- displays LAN information for the BMC
* **chassis_status** -- displays the node's current chassis status
* **power_status** -- displays the node's current power status

and the STATE value (passed via the '--power-state' command-line flag) must be one of the following values:

* **on** -- used to turn on a (powered off) node
* **off** -- used to turns off a (powered on) node
* **cycle** -- used to power-cycle a node that is currently powered on
* **reset** -- used to perform a hard-reset on a node that is currently powered on

Finally, as can be seen above, there are additional options that must be provided when registering a BMC with Occam.  The complete usage for the registration command is as follows:
```bash
occam bmc register (options...)
   -i, --ip_address (IP_ADDR)      IP address for BMC being registered
   -m, --mac_address (MAC_ADDR)    MAC address for BMC being registered
```
The two parameters (which must be supplied as part of the registration request) are the IP address and MAC address for the BMC being registered.  These two values are assumed to be unique for each BMC registered with the Occam server.

## The bmc RESTful API

The bmc RESTful API is provided via three resources ('/bmc', '/bmc/{UUID}', and '/bmc/register').  The operations that are supported for each of these resources are as follows:

* **GET /bmc** -- used to get a summary view of all of the BMC instances that are registered with the system (including the current power state and the motherboard serial number of the node they are attached to)
* **GET /bmc/{UUID}** -- used to get the details of a specific BMC instance (by UUID); the details returned are the same as those returned in the previous operation, but the values returned are only those for that specific BMC instance.
* **GET /bmc/{UUID}?filter_str** -- used to get additional information from a specific BMC instance (by UUID); the filter_str values supported by this resource all take the form of 'query=(query_str)' where the (query_str) value is one of the following:  'chassis_status', 'enables', 'fru_print', 'guid', 'info', 'lan_print', or 'power_status'
* **POST /bmc/register?json_hash=(JSON_STR)** -- used to register a new BMC instance with the Occam server; the 'ip_address' and 'mac_address' values for the registration request are included in the (JSON_STR) value, a URL-encoded JSON hash that must be included as part of the registration request.  An example of such a json_hash value would be something like the following string:
```html
%7B%22mac_address%22:%2200:0c:29:ec:67:fc%22,%22ip_address%22:%22127.0.0.4%22%7D
```
which is the URL-encoded version of the following JSON string representation of a hash map containing values for these two parameters:
```json
{"mac_address":"00:0c:29:ec:67:fc","ip_address":"127.0.0.4"}
```
As was the case with the active_model slice, BMC instances are not meant to be modified or removed from the system, so none of the bmc RESTful API does not support an UPDATE or DELETE operation (nor are commands provided via the CLI to perform similar operations).