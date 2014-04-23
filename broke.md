A broker instance may be included as part of any policy that is defined within Hanlon. If a broker instance is included when creating a policy (see the description of the policy slice, below, for more information), that broker instance will be used to manage the process of handing off a node to an underlying DevOps system once the OS provisioning process defined by that policy is complete.

It should be noted here that once the node handoff process is complete, Hanlon assumes that any further management of the system will be done directly through the DevOps system. Once a node has been placed into that state, Hanlon will only intervene during the initial phases of the network boot process for that node (to supply the appropriate 'local boot' iPXE boot script).

At some later time, the DevOps system may remove the active_model that was bound to that node when a matching policy was found (via Hanlon’s RESTful API) and force a reboot of that node. This will, effectively, hand the node back over to Hanlon for management (and, perhaps, reprovisioning of a new OS instance), but until such a handoff from the DevOps system (back to Hanlon) occurs, that node is will remain under the exclusive control of the DevOps system.

## The broker CLI

The broker CLI provides users with the ability to view a summary of all of the broker targets currently available in the system, the details for a specific broker target, or a list of the broker plugins that are defined within Hanlon. Broker plugins are used when creating a new broker target (just as model templates are used when creating new model instances and policy templates are used when creating new policy instances). Currently, the only broker plugin in Hanlon is a 'puppet' plugin; but broker plugins for other DevOps systems will be added.

In addition to viewing the details of the broker targets or plugins in the system, the broker CLI provides users with the ability to create new broker targets, update existing broker targets, and remove specific broker targets (or even to remove all existing broker targets). Help is also available for the CLI commands that make up the broker slice; providing user’s with usage assistance for the broker slice itself and for the sub-commands that are supported by that slice. Here is a high-level summary of the commands that are available via the broker CLI:
```bash
hanlon broker [get] [all]                   View all broker targets
hanlon broker [get] (UUID)                  View specific broker target
hanlon broker [get] plugin|plugins|t        View list of broker plugins
hanlon broker add (options...)              Create a new broker target
hanlon broker update (UUID) (options...)    Update a specific broker
hanlon broker remove (UUID)                 Remove existing broker(s)
```
It should be noted here that there are a set of options that must be specified when adding a new broker target to the system using the 'broker add' command. Those options are shown below:
```bash
hanlon broker add (options...)
    -p, --plugin BROKER_PLUGIN       The broker plugin to use. 
    -n, --name BROKER_NAME           The name for the broker target. 
    -d, --description DESCRIPTION    A description for the broker target. 
    -h, --help                       Display this screen.
```
The value for the 'plugin' argument (above) must be the name of one of the available broker plugins defined in the system. The names of the currently defined broker plugins can be easily obtained using the `hanlon broker plugins` command.

It should also be noted here that when this functionality is accessed using the CLI, Hanlon will walk through an interactive script (much like is done in the case of the model slice) in order to gather additional (broker-plugin-specific) meta-data. For the 'puppet' broker plugin, the following parameters are defined using that interactive script:

* **server** -- used to define the location of the Puppet master for a given node. It should be noted here that a hostname should be used for the value of this parameter (rather than an IP address) because this server value is also assumed to be the value of the 'ca_server', which will be embedded in the certificates used for authentication between puppet and the node). If the default value (an empty string) is used for this parameter, then the default hostname used by Puppet will be assumed for this hostname parameter.
* **broker_version** -- used to define the version of Puppet that should be installed locally on the node (using a 'gem install' command) during the node handoff process. If the default value (an empty string) is used for the value of this parameter, then the latest version of Puppet will be installed locally (whatever version that may be).

For the 'chef' broker plugin, the following parameters are defined using a similar interactive script:

* **chef_server_url** -- the URL of where the Chef server can be found
* **chef_version** -- the Chef version to install locally on the node (used in the 'gem install' command to constrain the version of Chef that is installed)
* **validation_key** -- the contents of the validation.pem file, followed by a blank line
* **validation_client_name** -- the validation client name
* **bootstrap_environment** -- the Chef environment in which the chef-client will run
* **install_sh_url** -- the Omnibus installer script URL
* **chef_client_path** -- an alternate path to the chef-client binary
* **base_run_list** -- an optional run_list of common base roles.

Like the 'broker add' command, there are a set of options to the 'broker update' command, and the user must also supply one or more of those options when updating a broker instance (each option that is supplied corresponds to a meta-data value that will be updated in the specific broker target instance). Those options are shown here:
```bash
hanlon broker update (UUID) (options...)
    -n, --name BROKER_NAME           New name for the broker target. 
    -d, --description DESCRIPTION    New description for the broker target. 
    -c, --change-metadata            Used to trigger a change in the broker's meta-data 
    -h, --help                       Display this screen.
```
As you can see, except for the broker plugin, any of the meta-data values associated with a broker target can be changed via this 'broker update' command. Changes to the underlying (broker-plugin-specific) meta-data can be made interactively by including the '-c' or '--change-metadata' flag in the 'broker update' command. Changes to the broker plugin type are not possible using a 'broker update' command. Instead, the user must create a new broker target of the correct type (and, possibly, the remove the old broker target).

## The broker RESTful API

The broker RESTful API is provided via two resources ('/broker' and '/broker/{UUID}'). There is also a third, associated resource provided through the RESTful API ('/broker/plugins') that can be used to obtain a list of available broker plugins. The operations that are supported for each of these resources are as follows:

* **GET /broker** -- used to get a summary view of all of the broker instances that are registered with the system
* **GET /broker/{UUID}** -- used to get the details of a specific broker instance (by UUID); the details returned are the same as those returned in the previous operation, but the values returned are only those for the specified broker instance.
* **GET /broker/plugins** -- used to obtain a list of the broker plugins that are available in the system; a broker plugin is used to define a template for a specific DevOps system (eg. Puppet or Chef), and one of these plugins must be specified when creating a new broker instance.
* **GET /broker/plugins{name}** -- used to obtain a specific broker plugins that are available in the system (by name);the details returned are the same as those returned in the previous operation, but the value returned for the specified broker plugin by name.
* **POST /broker** -- used to create a new broker instance; the body of this POST should be a JSON hash containing all of the parameters necessary to create a new instance:

    ```json
    {"name":"puppet_broker","plugin":"puppet","description":"Puppet Broker","req_metadata_hash":{"server":"puppet.localdomain.com","broker_version":"3.0.1"}}
    ```

    *Note; the req_metadata_hash part of this JSON hash contains the same meta-data values that are gathered interactively whenever a broker instance of the specified type (based on the declared broker plugin) is created via the CLI*

* **PUT /broker/{UUID}** -- used to update an existing broker instance (by UUID) with new parameter values. It should be noted here that the 'plugin' value for a broker instance cannot be updated in this manner. As was the case with the POST request (above), the body of the PUT should be a JSON hash containing the parameters that are being updated (and their new values).
* **DELETE /broker/{UUID}** -- used to remove a specific broker instance; there is no 'remove all' operation supported via the broker RESTful API (it was felt that such an operation was too destructive to make available via REST).
