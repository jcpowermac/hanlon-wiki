# Active Model Slice

The `active_model` slice is used to view details on and remove Active Models in Razor.

The command `razor active_model help` will print the following help:

```
# razor active_model help
Active Model Slice:
Used to view active models, active model logs, and remove active models.
Policy commands:
	razor active_model                                     View all active models
	razor active_model (active model uuid) [options...]    View specific active model
	razor active_model logview                             Prints an aggregate active model log view
	razor active_model remove (active model uuid)|all      Remove an existing active model(s)
	razor active_model help                                Display this screen
```

## Operations supported

1. [Viewing Active Models](#viewing)
2. [Removing Active Models](#removing)

<a id="viewing"></a>
## Viewing Active Models
----

To view the current Active Models use the `razor active_model` command:

```
Active Models:
Label    State           Node UUID         Broker  Bind #           UUID           
Name   preinstall  3iMAFTzmTF1R1CaOu6dEmM  none    1        3oXn3tcHXTPA9eOlATJTj4  
Name   postintall  7jxp8F09QuMxkaRu9jpGDO  none    10       3oKM2hxcVfZmh3CDBB5UPe 
```


* __'Label'__
   * User defined label from root Policy Active Model was bound.
* __'State'__
   * The current state of the Active Model.
* __'Node UUID'__
   * The UUID of the Node this Active Model is bound to.
* __'Broker'__
   * Either the UUID of the Broker Target this Active Model is attached or none.
* __'Bind #'__
   * The number this Active Model is from the the root Policy. In the list above the first Active Model was the first to be bound from it's root Policy. The second Active Model was the tenth.
* __'UUID'__
   * The UUID of this Active Model.

To view details for a single Active Model provide the UUID of the Model with `razor active_model <UUID>`.

Example:

```
# razor active_model 3oKM2hxcVfZmh3CDBB5UPe
UUID =>  3oKM2hxcVfZmh3CDBB5UPe
Label =>  Name
Template =>  linux_deploy
Node UUID =>  7jxp8F09QuMxkaRu9jpGDO
Model Label =>  UbuntuPrecise
Model Name =>  ubuntu_precise
Current State =>  postinstall
Broker Target =>  none
Bound Number =>  1
Bind Time =>  13:35:02 07-03-2012
```

You can also view the Active Model logs for a specific Active Model by adding the '--logs' switch:

```
# razor active_model 3oKM2hxcVfZmh3CDBB5UPe --logs
Label     State           Node UUID         Broker  Bind #           UUID           
Name   postinstall  7jxp8F09QuMxkaRu9jpGDO  none    1       3oKM2hxcVfZmh3CDBB5UPe  

         State                 Action                   Result                Time     Last     Total            Node           
init                     mk_call             n/a                            13:35:12  0 sec    0 sec    7jxp8F09QuMxkaRu9jpGDO  
init                     boot_call           Starting Ubuntu model install  13:35:34  22 sec   22 sec   7jxp8F09QuMxkaRu9jpGDO  
init                     preseed_file        Replied with preseed file      13:35:59  25 sec   47 sec   7jxp8F09QuMxkaRu9jpGDO  
init=>preinstall         preseed_start       Acknowledged preseed read      13:36:01  2 sec    49 sec   7jxp8F09QuMxkaRu9jpGDO  
preinstall=>postinstall  preseed_end         Acknowledged preseed end       13:42:23  6.4 min  7.2 min  7jxp8F09QuMxkaRu9jpGDO  
postinstall              postinstall_inject  n/a                            13:42:24  1 sec    7.2 min  7jxp8F09QuMxkaRu9jpGDO  
postinstall              boot_call           Replied with os boot script    13:42:47  23 sec   7.6 min  7jxp8F09QuMxkaRu9jpGDO  
postinstall              set_hostname_ok     n/a                            13:43:03  16 sec   7.8 min  7jxp8F09QuMxkaRu9jpGDO  
postinstall              sources_fix         n/a                            13:43:04  1 sec    7.9 min  7jxp8F09QuMxkaRu9jpGDO  
postinstall              apt_update_ok       n/a                            13:44:08  1.1 min  8.9 min  7jxp8F09QuMxkaRu9jpGDO  
postinstall              apt_upgrade_ok      n/a                            13:45:04  56 sec   9.9 min  7jxp8F09QuMxkaRu9jpGDO 
```

You can also use `razor active_model logview` to summarize all of the current Active Model logs in order by date/time:

```
# razor active_model logview
All Active Model Logs:
         State                 Action                   Result                Time     Last     Total            Node           
init                     mk_call             n/a                            13:35:12  0 sec    0 sec    7jxp8F09QuMxkaRu9jpGDO  
init                     mk_call             n/a                            13:35:12  0 sec    0 sec    3iMAFTzmTF1R1CaOu6dEmM  
init                     boot_call           Starting Ubuntu model install  13:35:34  22 sec   22 sec   7jxp8F09QuMxkaRu9jpGDO  
init                     boot_call           Starting Ubuntu model install  13:35:34  22 sec   22 sec   3iMAFTzmTF1R1CaOu6dEmM  
init                     preseed_file        Replied with preseed file      13:35:59  25 sec   47 sec   7jxp8F09QuMxkaRu9jpGDO  
init                     preseed_file        Replied with preseed file      13:36:00  26 sec   48 sec   3iMAFTzmTF1R1CaOu6dEmM  
init=>preinstall         preseed_start       Acknowledged preseed read      13:36:01  2 sec    49 sec   7jxp8F09QuMxkaRu9jpGDO  
init=>preinstall         preseed_start       Acknowledged preseed read      13:36:02  2 sec    50 sec   3iMAFTzmTF1R1CaOu6dEmM  
preinstall=>postinstall  preseed_end         Acknowledged preseed end       13:42:23  6.4 min  7.2 min  7jxp8F09QuMxkaRu9jpGDO  
postinstall              postinstall_inject  n/a                            13:42:24  1 sec    7.2 min  7jxp8F09QuMxkaRu9jpGDO  
postinstall              boot_call           Replied with os boot script    13:42:47  23 sec   7.6 min  7jxp8F09QuMxkaRu9jpGDO  
postinstall              set_hostname_ok     n/a                            13:43:03  16 sec   7.8 min  7jxp8F09QuMxkaRu9jpGDO  
postinstall              sources_fix         n/a                            13:43:04  1 sec    7.9 min  7jxp8F09QuMxkaRu9jpGDO  
preinstall=>postinstall  preseed_end         Acknowledged preseed end       13:43:07  7.1 min  7.9 min  3iMAFTzmTF1R1CaOu6dEmM  
postinstall              postinstall_inject  n/a                            13:43:08  1 sec    7.9 min  3iMAFTzmTF1R1CaOu6dEmM  
postinstall              boot_call           Replied with os boot script    13:43:29  21 sec   8.3 min  3iMAFTzmTF1R1CaOu6dEmM  
postinstall              set_hostname_ok     n/a                            13:43:44  15 sec   8.5 min  3iMAFTzmTF1R1CaOu6dEmM  
postinstall              sources_fix         n/a                            13:43:45  1 sec    8.6 min  3iMAFTzmTF1R1CaOu6dEmM  
postinstall              apt_update_ok       n/a                            13:44:08  1.1 min  8.9 min  7jxp8F09QuMxkaRu9jpGDO  
postinstall              apt_upgrade_ok      n/a                            13:45:04  56 sec   9.9 min  7jxp8F09QuMxkaRu9jpGDO  
postinstall              apt_update_ok       n/a                            13:45:04  1.3 min  9.9 min  3iMAFTzmTF1R1CaOu6dEmM
```

<a id="removing"></a>
 ## Removing Active Models
----   
Removing an Active Model frees a Node from its binding to Policy and Model. This means the next time a Node boots into Razor it will reevaluate against the Policy rules and reprovision. This can be useful for reseting Nodes or reprovisioning Nodes with new Models.

To remove an specific active model use the command `razor active_model remove <UUID>`.

Example:

    # razor active_model remove 3oKM2hxcVfZmh3CDBB5UPe
    Active_model remove_active_model_by_uuid
     Active model 3oKM2hxcVfZmh3CDBB5UPe removed

  
To remove all Active Models use the command `razor active_model remove all`.

Example:

```
razor active_model remove all
Active_model remove_active_model_all
 All active models removed 
```