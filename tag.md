A tag is simply a string label that can be applied to one or more nodes within Razor.  Tags are applied to nodes based on the rules defined in the set of tag matchers that are contained within those tags.  A node is 'tagged' with the string contained within a given tag if the properties of those nodes match the set of rules defined within the set of tag matchers that have been added to that tag.  It should be noted that there is no 'action' within Razor that will tag a given node with a given tag; the application of a tag (or set of tags) to a node is a dynamic process that occurs automatically whenever a node is retrieved from Razor.  As such, the set of tags that have been applied to a node can (and typically will) change over time as new tags and tag matchers are defined.  It should also be apparent from the previous discussion that if there are no tag matchers defined for a given tag, then that tag is simply a string that will never be applied to a node by Razor.

As can be seen in the documentation for the policy slice, these tags are used when defining new policy instances.  If the tags that have been applied to a node match the tags in a given policy instance, then that policy instance MAY be applied to that node.  Whether or not that policy instance actually will be applied to that node depends on that policy instance's position within the policy table (and whether or not there is another policy with a higher priority that also matches the tags that have been applied to that node).  Like the process of tagging a node, the mapping of policies to nodes is a dynamic process that occurs automatically, with Razor evaluating the mapping of policies a given node every time a node checks in with Razor (binding a given node to the appropriate policy automatically if there is a policy that matches that node at that time).

In addition to their use within policy instances (which we just described), tags have a second use within Razor.  The tags that have been applied to a node are passed on to the DevOps system during the node handoff process, providing the DevOps system with a set of labels for each node that it receives that it can use to make further decisions as to how each node that it receives from Razor should be configured (in the post-node-handoff phase).  This pattern becomes even more powerful when one considers that the tags, tag matchers, and policies themselves could, quite possibly, have been configured from within the DevOps system using the RESTful API that is provided by Razor.  It is this combination of tags, tag matchers, policies, and the DevOps system that make Razor such a powerful and dynamic system for managing a set of nodes in a typical next-generation datacenter.

## The tag CLI

The tag CLI can best be thought of in two parts.  The first part of this CLI is used to view, create, update, and delete tags from the system.  Here is a high-level summary of that first set of commands:
```
razor tag [get] [all]                           View Tag summary
razor tag [get] (UUID)                          View details of a Tag
razor tag add (...)                             Create a new Tag
razor tag update (UUID) (...)                   Update an existing Tag
razor tag remove (UUID)|all                     Remove existing Tag(s)
```
As you can see from the usage shown above, there are options required when creating a new tag instance, which are shown using the string '(...)' in the 'tag add' command, above.  Those options are shown below:
```
Usage: razor tag add (options...)
   -n, --name NAME                  Name for the tagrule being created
   -t, --tag TAG                    Tag for the tagrule being created
```
As part of the 'tag update' command the user must also supply one or more of these two options, as is shown here:
```
Usage: razor tag update (UUID) (options...)
   -n, --name NAME                  New name for the tagrule being updated.
   -t, --tag TAG                    New tag for the tagrule being updated.
```
The second part of the tag CLI is used to view, create, update, and delete tag matchers from a given tag instance.  Here is a high-level summary of that second set of commands:
```
razor tag (T_UUID) matcher [get] (UUID)         View Tag Matcher details
razor tag (T_UUID) matcher add (...)            Create a new Tag Matcher
razor tag (T_UUID) matcher update (UUID) (...)  Update a Tag Matcher
razor tag (T_UUID) matcher remove (UUID)        Remove a Tag Matcher
```
As you can see from the usage shown above, there are options required when adding a tag matcher to an existing tag.  These options, which are shown using the string '(...)' in the 'tag matcher add' command usage shown above, are as follows:
```
Usage: razor tag (T_UUID) matcher add (options...)
   -k, --key KEY_FIELD        The node attribute key to match against.
   -c, --compare METHOD       The comparison method to use ('equal'|'like').
   -v, --value VALUE          The value to match against
   -i, --invert VALUE         Invert the match (true if key does not match).
```
In this command, the invert parameter is optional (and defaults to 'false').  If set to 'true', this flag will reverse the sense of the comparison operation defined by this tag matcher (making 'equals' into 'not equals' or 'like' into 'not like').  The other three parameters, which MUST be included when creating a new tag matcher using this command, are defined as follows:

* **key** --  the name of the node attribute containing the value that is being matched
* **compare** -- the comparison method to use ('equal' returns true if the node's attribute value is equal to the value defined in the tag matcher instance, 'like' returns true if the node's attribute value matches the regular expression defined by the tag matcher instance's value parameter)
* **value** -- the value to compare against

As part of the 'tag matcher update' command the user must also supply one or more of these same options (as is shown here):
```
Usage: razor tag (T_UUID) matcher update (UUID) (options...)
   -k, --key KEY_FIELD     The new node attribute key to match against.
   -c, --compare METHOD    The new comparison method to use ('equal'|'like').
   -v, --value VALUE       The new value to match against.
   -i, --invert VALUE      Invert the match (true|false).
```
This command will update the specified tag matcher instance to use the new parameter values that are included in the command.

## The tag RESTful API

The tag RESTful API is provided via four resources ('/tag', '/tag/{UUID}', '/tag/{T_UUID}/matcher', and '/tag/{T_UUID}/matcher/{UUID}').  As was the case for the CLI (above), it's best to think of the tag RESTful API as consisting of two parts.  The first part, which is focused on tag-related operations, can be summarized as follows:

* **GET /tag** -- used to get a summary view of all of the tag instances that are registered with the system
* **GET /tag/{UUID}** -- used to get the details of a specific tag instance (by UUID); the details returned are the same as those returned in the previous operation, but the values returned are only those for that specific tag instance.
* **POST /tag?json_hash=(JSON_STR)** -- used to create a new tag instance; the 'name' and 'tag' values that are necessary for this operation MUST included in the (JSON_STR) value, a URL-encoded JSON hash that must be included as part of the tag creation request.  An example of such a json_hash value would be something like the following string:
```html
%7B%22name%22%3A%22Test%20Tag%22%2C%20%22tag%22%3A%22test_tag%22%7D%0A
```
which is the URL-encoded version of the following JSON string representation of a Hash map containing values for these two parameters:
```json
{"name":"Test Tag", "tag":"test_tag"}
```
* **PUT /tag/{UUID}?json_hash=(JSON_STR)** -- used to update an existing tag instance with new parameters.  One or more of the parameters that can be included in the json_hash argument of a POST to the same resource ('name' or 'tag') must be included in the json_hash argument that is part of this request.
* **DELETE /tag/{UUID}** -- used to remove a specific tag instance; there is no 'remove all' operation supported via the model RESTful API (it was felt that such an operation was too destructive to make available via REST).

The second part of the tag RESTful  API, which is focused on tag matchers, is similar.  The commands supported by this part of the tag RESTful API can best be summarized as follows:

* **GET /tag/{T_UUID}/matcher/{UUID}** -- used to get the details of a specific tag matcher instance (by UUID); note that there is no operation currently defined to get a list of all of the tag matchers for a given tag.
* **POST /tag/{T_UUID}/matcher?json_hash=(JSON_STR)** -- used to create a new tag matcher instance for the tag instance specified by the tag UUID value {T_UUID}; the 'key', 'compare', and 'value' fields that are necessary for this operation MUST included in the (JSON_STR) value, a URL-encoded JSON hash that must be included as part of the tag creation request.  The 'invert' field may also be provided (but the default value of 'false' is used for this field if it is not specified as part of this operation).  An example of such a json_hash value would be something like the following string:
```html
%7B%22key%22%3A%22mk_hw_nic_count%22%2C%22compare%22%3A%22equal%22%2C%22value%22%3A%222%22%7D%0A
```
which is the URL-encoded version of the following JSON string representation of a Hash map containing values for these three parameters:
```json
{"key":"mk_hw_nic_count","compare":"equal","value":"2"}
```
* **PUT /tag/{T_UUID}/matcher/{UUID}?json_hash=(JSON_STR)** -- used to update an existing tag matcher instance with new parameters.  One or more of the parameters that can be included in the json_hash argument of a POST to the same resource ('key', 'compare', 'value' or 'invert') must be included in the json_hash argument that is part of this request.
* **DELETE /tag/{T_UUID}/matcher/{UUID}** -- used to remove a specific tag matcher instance from a specific tag; there is no 'remove all' operation supported via the tag (matcher) RESTful API (it was felt that such an operation was too destructive to make available via REST).