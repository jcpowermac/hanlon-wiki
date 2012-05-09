# Razor Slices - CLI

In Razor, a *Slice* represents a unit of functionality. Slices are automatically loaded into Razor when the server starts. To determine which slices are available in Razor, simply run the *razor* command with no additional arguments. The command will produce a list of loaded slices in the output:

    root@ubuntu-server:~# razor
    
    ProjectRazor - v0.1.6.0
    
    EMC Corporation
    
      Usage:
      project_razor [slice name] [command argument] [command argument]...
       Switches:
         --debug        : Enables printing proper Ruby stacktrace
         --verbose      : Enables verbose object printing
         --no-color-out : Disables console color. Useful for script wrapping.
    
    Loaded slices:
      [bmc] [broker] [image] [log] [model] [policy]
      [tagmatcher] [tagrule]

## Default Slices

The following slices are available in a default Razor installation:

- **bmc** &ndash; interact with the Baseboard Management Controllers (BMC) installed on the hardware nodes.
- **boot** &ndash;
- **broker** &ndash; manages remote broker plugin and broker target such as Puppet and ESX.
- **config** &ndash; display and manage the current Razor configuration.
- **image** &ndash; manage and load images into Razor which are used during the (iPXE) boot process.
- [**log**](/lynxbat/Razor/wiki/The%20Logviewer%20Slice) &ndash; view and filter the Razor server's logfiles.
- **model** &ndash; Used to govern the process of provisioning a node with an image; includes a deployment template containing os image, answer file, and user supplied configuration data. 
- **node** &ndash; display node information such as meta-data, last checkin time, current state, associated tags, etc.
- **policy** &ndash; Used to define policies that will map models to nodes based on tagrules
- **tagrule** &ndash; Used to define tag rules.
- **tagmatcher** &ndash; Used to define rules that will match tags to nodes.

As you can see from the output of the Razor command (shown above), it is relatively simple to use these slices from the command-line. You simply add a named slice after the razor command (after any switches that you might want to include, like "--verbose" or "--no-color-out") and you will execute the functionality of that slice.  Slices may also provide a web-based (RESTful services) interface that can be used to interact with that slice via HTTP/HTTPS. For the slices that do provide such an interface, the RESTful API will directly mimic the command-line interface (or CLI).

## CLI and REST interface

Razor slice may provide both CLI and/or REST API interface. As an example, here is the result of running the *razor config* command via the CLI:

    root@ubuntu-server:~# razor config
    ProjectRazor Config:
        admin_port: 8025
        api_port: 8026
        default_ipmi_password: ipmi_password
        default_ipmi_power_state: off
        default_ipmi_username: ipmi_user
        force_mk_uuid:
        image_svc_host: 192.168.5.2
        image_svc_path: /root/Razor/image
        image_svc_port: 8027
        mk_checkin_interval: 60
        mk_checkin_path: /razor/api/node/checkin
        mk_checkin_skew: 5
        mk_fact_excl_pattern: (^facter.*$)|(^id$)|(^kernel.*$)|(^memoryfree$)|(^operating.*$)|(^osfamily$)|(^path$)|(^ps$)|(^ruby.*$)|(^selinux$)|(^ssh.*$)|(^swap.*$)|(^timezone$)|(^uniqueid$)|(^uptime.*$)|(.*json_str$)
        mk_log_level: Logger::DEBUG
        mk_register_path: /razor/api/node/register
        mk_uri: http://192.168.5.2:8026
        persist_host: 127.0.0.1
        persist_mode: mongo
        persist_port: 27017
        persist_timeout: 10
        register_timeout: 120

*razor config* also provides REST interface which can be accessed through nodejs via the api_port: http://localhost:${api_port}/razor/api/${slice_name}:

    $ curl http://localhost:8026/razor/api/config
    {"@image_svc_host":"192.168.232.141","@persist_mode":"mongo","@persist_host":"127.0.0.1","@persist_port":27017,"@persist_timeout":10,"@admin_port":8025,"@api_port":8026,"@image_svc_port":8027,"@mk_checkin_interval":60,"@mk_checkin_skew":5,"@mk_uri":"http://192.168.232.141:8026","@mk_register_path":"/razor/api/node/register","@mk_checkin_path":"/razor/api/node/checkin","@mk_fact_excl_pattern":"(^facter.*$)|(^id$)|(^kernel.*$)|(^memoryfree$)|(^operating.*$)|(^osfamily$)|(^path$)|(^ps$)|(^ruby.*$)|(^selinux$)|(^ssh.*$)|(^swap.*$)|(^timezone$)|(^uniqueid$)|(^uptime.*$)|(.*json_str$)","@mk_log_level":"Logger::ERROR","@image_svc_path":"/mnt/nfs/Razor/image","@register_timeout":120,"@force_mk_uuid":"","@default_ipmi_power_state":"off","@default_ipmi_username":"ipmi_user","@default_ipmi_password":"ipmi_password"}

When invoking the config slice via CLI, razor returns a hash of server configuration in name/value pair. When invoking the config slice via the REST API, razor presents the same data as the CLI, but the server configuration hash is returned in JSON format instead. This data format is used for all slices that provide both a CLI and a REST interface.
