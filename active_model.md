As was described previously, the active_model slice is primarily used to view information about the Active Models that have been bound to Nodes in the system.  When a given Policy is mapped to a specific Node (based on the tags that have been applied to that node and the tags associated with that policy), a copy of that Policy instance is made and that copy is bound to that Node as an Active Model.  Razor then uses that Active Model (and the state associated with the Node) to determine what should be done whenever that Node checks in.

## The active_model CLI

The active_model CLI provides users with commands to get a list of the active_model instances in the system, get the details of a specific active_model instance, view the logs for a specific active_model instance, print an aggregate view of the logs of all of the active_model instances in the system to the shell/console being used, or remove active_model instances from the system (either one by one or all at once).  The usage for the active_model CLI is as follows:
```bash
razor active_model [get] [all]          View all active models
razor active_model [get] (UUID) [logs]  View specific active model (log)
razor active_model logview              Prints an aggregate log view
razor active_model remove (UUID)|all    Remove existing active model(s)
```
## The active_model RESTful API

The active_model RESTful API is provided via two resources ('/active_model' and '/active_model/{UUID}').  The operations that are supported for each of these resources are as follows:

* **GET /active_model** -- used to get a summary view of all of the active_model instances in the system
* **GET /active_model/{UUID}** -- used to get the details of a specific model instance (by UUID); the details returned include information about the model that is contained within that active_model instance, the node that it is bound to, and a complete log of the state-machine transitions for that node since the active_model was bound to it.
* **DELETE /active_model/{UUID}** -- used to remove a specific active_model instance (by UUID), which 'unbinds' that active_model instance from the node that it was bound to; the effect of this operation is to force a reevaluation of that node against the current policy table the next time that node checks in with Razor (which may result in a new OS instance being installed on that node if there is a policy instance that matches that node in the current policy table)

It should be noted here that while users CAN remove all active_model instances from the system (in effect, unbinding all nodes from their active_model instances), this functionality is not supported by the active_model RESTful API (only via the CLI).  It should also be noted here that active_model instances are never created or modified by end-users (only by Razor), so there is no support for POST or PUT operations provided by either of these two RESTful services endpoints (nor is there support from the equivalent 'add' or 'update' operations in the active_model CLI.