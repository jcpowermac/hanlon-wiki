Occam’s node slice is used in two ways within Occam.  First, the node slice provides an interface that can be used view information about the nodes that are currently in the system, either in the form of a summary of the current nodes in the system (including their status, last checkin time, and any tags that have been applied to them) or in the form of a detailed set of information about a given node (including information that was gathered from that node using a Occam Microkernel instance).  Second, the node slice provides the interface that is used by the Occam Microkernel instances themselves to checkin with Occam (periodically) and to register with Occam; a process that can be triggered by the Microkernel instances themselves (when they detect that the underlying facts that they are reporting to Occam about the node they have been deployed to have changed) or in response to a command from Occam (included as part of the response from Occam to a 'node checkin' request) instructing them to (re)register with the Occam server.

It should be noted here that there is no ability to update a node (or remove a node) provided as part of the node slice (in either the node slice’s CLI or the node slice’s RESTful API).  Nodes are created during the initial node checkin/registration process and updated periodically by their Microkernel instances using that same process.  Nodes are only removed from the system after they have been inactive for a specified period of time (the 'node_expire_timeout', which defaults to 300 seconds, or 5 minutes, in the current version of Occam).  If a node remains inactive (i.e. does not checkin with Occam) for a period of time that exceeds this this 'node_expire_timeout' period it will be removed from the system by Occam (and forced to re-register as a new node instance if it checks in again in the future).

## The node CLI

The node CLI provides a set of 'view-only' commands, that give the users the ability to 'get' information about a node (or set of nodes) from Occam:
```
occam node [get] [all]                      Display list of nodes
occam node [get] (UUID)                     Display details for a node
occam node [get] (UUID) [--field,-f FIELD]  Display node's field values
```
As you can see, there are options provided to get a summary view of all of the nodes currently registered with Occam (and their status) and to get a detailed view of a specific node (by UUID).  Occam also provides users with the ability to display a list of all of the attributes that have been gathered from a specific node (and reported to Occam as part of the node registration process) using the 'occam node (UUID) --field attributes' command or to view a list of the hardware ID values that were reported to Occam by the node’s Microkernel during the node checkin/registration process using the 'occam node (UUID) --field hardware_ids' command.  As is the case with many of the Occam CLI commands, aliases for these two field names are supported ('attrib' instead of 'attributes' and 'hardware' or 'hardware_id' instead of 'hardware_ids').

## The node RESTful API

The node RESTful API is provided via two resources ('/node' and '/node/{UUID}').  The operations supported by these resources are as follows:

* **GET /node** -- used to get a summary view of all of the nodes that are registered with the system
* **GET /node/{UUID}** -- used to get the details of a specific node (by UUID); the details returned are the same as those returned in the previous operation, but the values returned are only those for that specific node instance.