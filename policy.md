A Policy is used to map a specific model to a node in Occam (based on the Tags that Occam assigns to that Node).  There are typically a number of policy instances defined within Occam, and the that of policy instances form a Policy Table.  This policy table is like a firewall rules table, where every policy in the table has a policy number, and lower numbers have a higher priority than higher numbers.  The first (highest priority) policy that matches a given node (based on the tags that have been assigned to that node) is bound to that particular node.  Until a policy is bound to a node, that node will remain in an unbound state (and a discovery Microkernel will be deployed to it whenever that node boots).  Once a policy is bound to a node (in the form of an active_model), that node will remain bound to that policy until the active_model instance that was created during the policy binding process is removed from the system.

## The policy CLI

The policy CLI provides users with the ability to create new policy instances, view a summary list of the policies that have been created (or view the details of a specific policy), update a specific policy, or remove a specific policy (or all policies) from the system.  Here is a high-level summary of the commands that are available via the policy CLI:
```
occam policy [get] [all]                      View all policies
occam policy [get] (UUID)                     View a specific policy
occam policy [get] templates|types            View available policy templates
occam policy add (options...)                 Create a new policy
occam policy update (UUID) (options...)       Update an existing policy
occam policy remove (UUID)|all                Remove existing policy(s)
```
As you can see from the usage shown above, there are options that are required when creating a new policy instance using the 'policy add' command.  Those options are shown below:
```
Usage: occam policy add (options...)
   -p, --template TEMPLATE_NAME     The policy template name to use.
   -l, --label POLICY_LABEL         A label to name this policy.
   -m, --model-uuid MODEL_UUID      The model to attach to the policy.
   -b, --broker-uuid BROKER_UUID    The broker to attach to the policy.
   -t, --tags TAG{,TAG,TAG}         Policy tags. Comma delimited.
   -e, --enabled ENABLED_FLAG        Should policy be enabled (true|false)?
   -x, --maximum MAXIMUM_COUNT      Sets the policy maximum count for nodes.
```
there are three optional arguments included in this list:

* **broker_uuid** -- this parameter is used to define a broker that should be used to handle the node handoff process (once the OS install process that will be driven by this policy is complete); it has a default value of 'none' (meaning that there is no broker to hand the system off to after the OS install process complete).
* **maximum** -- this parameter is used to define a maximum number of nodes that should be bound using the policy that is being created; it has a default value of '0' (zero), which means that the policy instance being created has no maximum count.  If a non-zero maximum count is defined, then the policy will only be bound to that many nodes.  Once the maximum count is reached, the policy instance will, effectively, be disabled (and no further nodes will be bound to that policy).
* **enabled** -- this flag is optional, and if it is included the policy will be created with its 'enabled' state set to the value passed through using its ENABLED_FLAG value ('true' or 'false'); if this flag is not included, then the policy will be created with this 'enabled' state set to 'false' (the default value for this parameter), which leaves the creates a disabled policy instance.

All other the other arguments shown above MUST be provided as part of the 'policy add' command for the command to be valid.  The value for the 'template' argument (above) must be the name of one of the available policy templates defined in the system.  The names of the currently defined policy templates can be easily obtained using the  occam policy templates command.  Currently there are two policy templates defined in occam ('linux_deploy' and 'vmware_hypervisor'), and the policy template that is chosen when creating a new policy instance must be consistent with the underlying model referred to by the 'model_uuid' parameter’s value.  Any new policy instance that is created is placed at the end of the policy table (below all other policy instances in terms of priority), but this placement can be changed later using a 'policy update' command (see below for more on this option).

As part of the 'policy update' command the user must also supply one or more options (each option that is supplied corresponds to the value of a parameter that will be updated in the specific policy instance).  Those options are shown here:
```
Usage: occam policy update (UUID) (options...)
   -l, --label POLICY_LABEL         A label to name this policy.
   -m, --model-uuid MODEL_UUID      The model to attached to the policy.
   -b, --broker-uuid BROKER_UUID    The broker attached to the policy.
   -t, --tags TAG{,TAG,TAG}         Policy tags. Comma delimited.
   -e, --enabled ENABLED_FLAG        Should policy be enabled (true|false)?
   -x, --maximum MAXIMUM_COUNT      Sets the policy maximum count for nodes.
   -n, --new-line-number NEW_NUM    Change policy rule number.
```
Except for the policy template, any of the parameters seen earlier when creating a new policy instance (label, model_uuid, broker_uuid, tags, maximum, and enabled) can be changed via this 'policy update' command.  Changes to the policy template require the creation of a new policy instance (and removal of the old policy instance if it is no longer needed).

It should also be noted here that there is one additional parameter in the 'policy update' command that did not appear in the 'policy add' command; the 'new_line_number' parameter.  This parameter can be used to move a policy to a new position in the policy table (the line numbers in the table start with zero and increase to [N-1], where N is the number of policies in the current policy table).  This approach (moving a policy to a specific line) was chosen over the previous approach (moving the policy up or down one line at a time) specifically so that the 'policy update' command would map well into a 'PUT' operation on the '/policy/{UUID}' resource in the RESTful API (see below).  In a proper RESTful API, any PUTs must be idempotent (meaning that the same operation can be invoked over and over with the same result).  This is not the case with a 'move up' or 'move down' operation, but is the case with a 'move to' operation.

## The policy RESTful API

The policy RESTful API is provided via two resources ('/policy' and '/policy/{UUID}').  There is also a third, associated resource provided through the RESTful API ('/policy/templates') that can be used to obtain a list of available policy templates.  The operations that are supported for each of these resources are as follows:

* **GET /policy** -- used to get a summary view of all of the policy instances that are registered with the system
* **GET /policy/{UUID}** -- used to get the details of a specific policy instance (by UUID); the details returned are the same as those returned in the previous operation, but the values returned are only those for that specific policy instance.
* **GET /policy/templates** -- used to obtain a list of the Policy Templates that are available in the system; one of these templates must be specified (by name) when creating a new policy instance.
* **GET /policy/templates{name}** -- used to obtain a specific Policy Template that are available in the system for a given template (by name).
* **POST /policy** -- used to create a new policy instance; the body of this POST should be a JSON hash containing all of the parameters necessary to create a new instance:
```json
{"label":"Test Policy", "model_uuid":"12Ek", "template":"linux_deploy", "tags":"two_disks,memsize_1GiB,nics_2"}
```
* **PUT /policy/{UUID}** -- used to update an existing policy instance with new parameter values. It should be noted here that the 'template' value for a policy instance cannot be updated in this manner. As was the case with the POST request (above), the body of the PUT should be a JSON hash containing the parameters that are being updated (and their new values).
* **DELETE /policy/{UUID}*** -- used to remove a specific policy instance; there is no 'remove all' operation supported via the model RESTful API (it was felt that such an operation was too destructive to make available via REST).

## Policy Binding

When a Node matches a Policy it is bound to it creating an Active Model assigned to that Node. The '#/Max' column when using `occam policy` displays the number of Active Models and the maximum allowed.

To control and view the Active Models use the `occam active_model` command or see the [Active Model](active_model) page.
