Models are used within Razor to manage the meta-data that is needed to successfully install an OS instance onto a node.  As such, they include references to an ISO image containing the contents of the OS instance to be installed and references to the meta-data that is needed to automatically install that OS instance.  Some of this meta-data is in the form of an ERB file containing  the appropriate “auto-install” script input for the OS in question (eg. kickstart for a Redhat-based OS, autoyast for a SLES-based OS, and preseed for a Debian-based OS), while other parts of this meta-data must be gathered interactively from the user during the model creation process (the hostname prefix, domain name, and root password to use for the newly-built node, for example).  Both of these types of meta-data are defined as part of the model itself.  Finally, models  are also where the state-machine transitions for an OS install process (and the callbacks that should be made at each transition) are defined.

Since all of these features (meta-data, auto-install script, state-machine transitions, and callbacks that should be made) change from one OS to another (and since there are variations in this process from one version of a given OS to another), new model instances are defined within Razor for each supported OS variant and version.  Defining a new model (to support a new OS instance and/or version) is a relatively simple process, involving the creation of a new Ruby class for that OS instance and a few ERB templates, but some care must be taken when defining a new model to ensure that the OS that is installed is as generic (and minimal) as possible.  Customization of the OS instance that is installed should really be left as something that is completed post-install, once the newly built node is handed off to the DevOps system.

## The model CLI

The model CLI provides users with the ability to create new model instances, view a summary list of the models that have been created (or view the details of a specific model instance), update a specific model instance, or remove a specific model instance (or all model instances) from the system.  Here is a high-level summary of the commands that are available via the model CLI:
```bash
razor model [get] [all]                 View all models
razor model [get] (UUID)                View specific model instance
razor model add (options...)            Create a new model instance
razor model update (UUID) (options...)  Update a specific model instance
razor model remove (UUID)|all           Remove existing model(s)
razor model --help                      Display this screen
```
As you can see from the usage shown above, there are options that are required when creating a new model instance using the “model add” command.  Those options are shown below:
```bash
Usage: razor model add (options...)
   -t, --template MODEL_TEMPLATE    The template to use for the new model.
   -l, --label MODEL_LABEL          The label to use for the new model.
   -i, --image-uuid IMAGE_UUID      The image UUID to use for the new model.
```
The value for the 'template' argument (above) must be the name of one of the available model templates defined in the system.  The names of the currently defined model templates can be easily obtained using the  razor model templates command.  Currently there are model templates defined for several Linux distributions (RedHat 6, Centos 6, Ubuntu Oneiric, Ubuntu Precise, Debian Wheezy, SLES 11, and OpenSUSE 12) and for one hypervisor distribution (ESXi 5), but additional models can easily be added to the system to support other OS/hypervisor variants so long as they support installation from their ISO images via an iPXE boot process.

As part of the 'model update' command the user must also supply one or more options (each option that is supplied corresponds to a meta-data value that will be updated in the specific model instance).  Those options are shown here:
```bash
Usage: razor model update (UUID) (options...)
   -l, --label MODEL_LABEL          The new label to use for the model.
   -i, --image-uuid IMAGE_UUID      The new image UUID to use for the model.
   -c, --change-metadata            Used to change the model's meta-data
```
Except for the model template, any of the values associated with a model (label, image, or model meta-data) can be changed via this 'model update' command.  Changes to the model template require the creation of a new model instance (and removal of the old model instance if it is no longer needed).

## The model RESTful API

The model RESTful API is provided via two resources ('/model' and '/model/{UUID}').  There is also a third, associated resource provided through the RESTful API ('/model/templates') that can be used to obtain a list of available model templates.  The operations that are supported for each of these resources are as follows:

* **GET /model** -- used to get a summary view of all of the model instances that are registered with the system
* **GET /model/{UUID}** -- used to get the details of a specific model instance (by UUID); the details returned are the same as those returned in the previous operation, but the values returned are only those for that specific model instance.
* **GET /model/templates** -- used to obtain a list of the Model Templates that are available in the system; as was mentioned previously, a model template defines a template for the installation of a specific OS instance (eg. RedHat 6 or Ubuntu Oneiric), and one of these templates must be specified when creating a model instance.
* **POST /model?json_hash=(JSON_STR)** -- used to create a new model instance; the 'label', 'image_uuid',  'template', and 'req_metadata_hash' that are necessary for this operation are included in the (JSON_STR) value, a URL-encoded JSON hash that must be included as part of the broker creation request.  An example of such a json_hash value would be something like the following string:
```html
%7B%22label%22%3A%22Test%20Model%22%2C%20%22image_uuid%22%3A%22OTP%22%2C%20%22template%22%3A%22ubuntu_oneiric%22%2C%20%22req_metadata_hash%22%3A%7B%22hostname_prefix%22%3A%22test%22%2C%22domainname%22%3A%22testdomain.com%22%2C%22root_password%22%3A%22test4321%22%7D%7D%0A
```
which is the URL-encoded version of the following JSON string representation of a hash map containing values for these four parameters:
```json
{"label":"Test Model", "image_uuid":"OTP", "template":"ubuntu_oneiric", "req_metadata_hash":{"hostname_prefix":"test","domainname":"testdomain.com","root_password":"test4321"}}
```
*Note; the req_metatdata_hash value is itself a hash, and it contains the same meta-data values that are gathered interactively whenever a model instance of the specified type (based on the declared model template) is created via the CLI.*
* **PUT /model/{UUID}?json_hash=(JSON_STR)** -- used to update an existing model instance with new parameters.  One or more of the 'label', 'image_uuid', or 'req-metadata_hash' values must be included in the json_hash argument that is part of this request.  It should be noted here that the 'template' value for a model instance cannot be updated in this manner.
* **DELETE /model/{UUID}** -- used to remove a specific model instance; there is no 'remove all' operation supported via the model RESTful API (it was felt that such an operation was too destructive to make available via REST).