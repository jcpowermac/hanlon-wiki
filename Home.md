Occam is an advanced provisioning application which can deploy both bare-metal and virtual systems. It's aimed at solving the problem of how to bring new metal into a state where your existing DevOps/configuration management workflows can take it over.

Newly added machines in a Occam deployment will PXE-boot from a special Occam Microkernel image, then check in, provide Occam with inventory information, and wait for further instructions. Occam will consult user-created policy rules to choose which preconfigured model to apply to a new node, which will begin to follow the model's directions, giving feedback to Occam as it completes various steps. Models can include steps for handoff to a DevOps system or to any other system capable of controlling the node (such as a vCenter server taking possession of ESX systems).

## Overview of the Occam CLI

For installation instructions see this page: [[Installation]]

The Occam CLI allows the user to interact with a local Occam server instance via a login/shell prompt. The typical Occam CLI command can most easily be seen by running the 'occam' command from the login/shell prompt on the Occam server (with no additional arguments):
```bash
occam [switches] [slice_name] [slice_operation] [UUID] [operation_flags] ...

Switches
   -v, --verbose           Enables verbose object printing
   -d, --debug             Enables printing proper Ruby stacktrace
   -n, --no-color          Disables console color. Useful for script wrapping.
   -h, --help              Display this screen

Loaded slices:
    [active_model] [broker] [image] [model] [node] [policy]
    [tag]
```
As you can see from the output of this command, there are currently seven visible slices defined within Occam:

* **active_model** -- Used to interact with the Active Models constructed when a Policy is mapped to a specific Node by Occam
* **broker** -- Used to manage the process of handing off a Node to a DevOps framework (like Puppet)
* **image** -- Used to manage the set of Images (Microkernel, OS, and Hypervisor ISOs) that are available within Occam. The Microkernel images are used during the Node discovery process (and may be used for other, more specialized tasks), while the OS and Hypervisor images are used in the construction of the Model instances that guide the Node Provisioning process.
* **model** -- Used to define/manage the Model instances that are mapped to Nodes by Policies in Occam; these Model instances are constructed using one of a fixed set of Model Templates
* **node** -- Used to view/manage Nodes within Occam. Nodes are physical or virtual servers that are managed by Occam, and they are neither created nor destroyed by end-users. Instead, Nodes are created by Occam during the Node Registration process and may be removed from Occam whenever Nodes are considered to have 'expired' due to inactivity. From an end-user's perspective, the primary functionality of this slice is to view the details of a specific Node (or to view a summary list of the Nodes that are currently available in the system)
* **policy** -- Used to map a Model to one or more Nodes based on the Tags that have been applied to those Nodes. The set of Policies in the system are organized as a policy table, with a priority assigned to each Policy in that table. The policy table is like a firewall rules table; the first Policy that matches a given Node will be applied to that Node (even if there is a second, lower-priority Policy that also matches based on the tags that have been applied to that Node)
* **tag** -- A logical label that is applied to any Node whose properties match that Tag according to the on the rules contained within that Tag's 'Tag Matchers' (there may be more than one Tag Matcher defined for a given Tag and it is the logical 'AND' of the rules in those matchers that determines whether or not a given Tag matches a given Node)

The supported operations for each slice and the command line flags that are allowed/required for a those slice operations will vary from slice to slice (and from operation to operation for a given slice), so we will leave the documentation of those operations/flags for later (in the documentation for the specific slices).

## Notes on the Occam RESTful API

Almost all of the functionality that is supported using these slices via the Occam CLI is also supported using the equivalent resources under REST (and vice-versa; there are a few exceptions to this rule for commands that should not be invoked through the RESTful API or the CLI). If a slice operations is supported through the CLI and the RESTful API, then the mapping from the RESTful API operation to the equivalent CLI operation is as follows:

<table>
    <tr>
        <th>RESTful VERB<\th>
        <th>Equivalent CLI Operation</th>
    </tr>
    <tr>
        <td>GET<\td>
        <td>get</td>
    </tr>
    <tr>
        <td>POST<\td>
        <td>add (or, for some slices, register)</td>
    </tr>
    <tr>
        <td>PUT<\td>
        <td>update</td>
    </tr>
    <tr>
        <td>DELETE<\td>
        <td>remove</td>
    </tr>
</table>

In Occam, the order of invocation has been inverted when compared to the Razor codebase that Occam was forked from.  In the new scheme, the flags and their values (if any) from a CLI slice call are converted into a JSON hash that is included in the body of the underlying RESTful call. We will show examples of this mapping within the documentation for each slice (below).

## Using Occam

In the pages that follow, we will describe the CLI and RESTful APIs for each slice (individually). For each slice we will describe what the slice is used for, how it works, and what sorts of operations it supports. We will also show examples of the use of each slice (via both the RESTful API and CLI for that slice). Notes will be provided where choices were made to make the new-style API consistent with the current best-practices for design and implementation of RESTful services, and the impact of those decisions will be highlighted (in terms of changes to the current CLI and/or RESTful APIs). There are a few conventions that are followed in the documentation that follows which should be noted here:

* In both the CLI and the RESTful API, any UUID value can be provided either in it's entirety (eg. 67kJLAp8kJIw8lKTRacK4T) or as a unique prefix (or leading substring) that is equivalent to that UUID value (eg. 67kJ or 67kJLA in the previous example); the user only needs to include enough leading characters from the UUID value in the slice command (or RESTful services request) to make the UUID value unique when compared to the other UUID values that are currently defined within the system.
* For the RESTful API documentation, a prefix of /occam/api is assumed for all of the URIs that are shown. This means that if an operation that is documented as 'GET /resource_name' the actual request sent to the Occam server would be 'GET /occam/api/resource_name'. This assumed prefix is not included in the actual documentation for brevity.
* The syntax /resource_name/{UUID} will be used in the RESTful API documentation (below) to indicate that a specific UUID value must be included as part of the RESTful services request that is submitted. The curly braces in that resource are not included in the actual request, instead they are meant to indicate that a specific UUID value should be used in the request. In the CLI documentation, the string '(UUID)' will be used to indicate that a specific UUID value is required by the CLI command being documented.
* If there are multiple options available for a command in the CLI (eg. you could use '--help' or '-h'), those options will be shown as two values separated by a 'logical OR' (i.e. as a string like '--help|-h' in the example just shown) or a comma ('--help, -h').
* Users can obtain help with the use of the slices supported by Occam (and with many of the commands supported by those slices) using the '--help' or '-h' flags when using the Occam CLI. An example of such usage would be 'occam model update --help', which provides help for the Model update command (see the description of the model slice, below, for more details on this command). These additional flags (for obtaining usage help) are common across all slices, so they will not be explicitly shown in the CLI documentation for each slice.
* Optional arguments to the CLI are shown surrounded by square brackets (eg. '[all]' or '[get]'); in some cases the command does the same thing without the optional argument ('[get]' is a good example of this for most slices), while in other cases the behavior of the command is different without the optional argument (eg. '[logs]' in the active_model slice, which can be included to get the log information for an active_model instance; without this argument the command returns the details for the instance, not the log information)
* Some of the CLI commands support the use of 'aliases' (mainly to shorten the commands when typed from the CLI). Good examples of this are the 'attributes' sub-command for the node slice (which can be abbreviated using the aliases 'attrib' or 'attribute'). Whenever such aliases exist they will be shown in the CLI documentation. However, it should be noted that the use of these aliases is not supported in the RESTful API for those slices (and any attempts to use them will result in an error).
* Many of the RESTful resources (active_model, broker, model, node, policy, and tag) support the use of a filter string in their default GET operation (which maps to a 'get all' operation in the underlying slice). This gives the user the ability to pass in a (URL encoded) set of name=value pairs as part of the GET request, and only resources who's 'name' field contains a matching 'value' will be returned (rather than returning all resources of a given type). The 'value' field can can take on one of the following forms:
    * regex:PATTERN -- if this form is used, the value in the resource is compared against the given regular expression PATTERN (the syntax used is the syntax for a typical Ruby regular expression pattern)
    * STRING_VALUE -- if this form is used, a string comparison is made between the value in the resource and the given STRING_VALUE
    * BOOLEAN_VAL -- this form is used whenever a boolean value (in the form of the String values 'true' or 'false') is used as the value for a field in a filter expression. In that case, a literal comparison between the value in the resource and the boolean equivalent to the given string is made. It should be noted that this means the string values 'true' and 'false' cannot be used for String comparisons with the fields in a resource (since they will be automatically converted to their boolean equivalents), but it is possible to use a regular expression to match the String values 'true' or 'false' to a string value from a resource (if such a comparison becomes necessary).

With those conventions in mind, the following pages describe the detailed documentation for the Occam CLI and RESTful API (slice-by-slice).

* The [[active_model]] slice
* The [[broker]] slice
* The [[config]] slice
* The [[image]] slice
* The [[model]] slice
* The [[node]] slice
* The [[policy]] slice
* The [[tag]] slice

## Alternative configuration and support

* [[Alternate PXE boot Options]]

## API Documentation

* [[Occam API Overview]]

And see here for a general overview of Occam's [[workflow]]