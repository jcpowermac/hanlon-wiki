# Razor Slices

In Razor, a *Slice* represents a unit of functionality. There are a number of Slices that are automatically loaded into Razor when the server starts, and it is a relatively simple matter to determine what slices are available at any given time by simply running the *razor* command (with no additional arguments). Doing so will produce output that looks something like the following output:

    root@ubuntu-server:~# razor
    
    ProjectRazor - v0.1.0.2
    EMC Corporation
    
    	Usage: 
    	project_razor [slice name] [command argument] [command argument]...
    	 Switches:
    		 --debug        : Enables printing proper Ruby stacktrace
    		 --verbose      : Enables verbose object printing
    		 --no-color-out : Disables console color. Useful for script wrapping.
    
    Loaded slices:
    	[config] [boot] [bmc] [tagrule] [tagmatcher] [system] 
    	[policy] [node] [model] [logviewer] [imagesvc] 
    	
    root@ubuntu-server:~# 

As you can see from that output, the current default set includes the following slices:

- **bmc** &ndash; Used to interact with the Baseboard Management Controllers (or BMCs) of the nodes in the system
- **config** &ndash; Used to view the current Razor configuration
- **node** &ndash; Used to view node-related information (meta-data discovered, last checkin time, current state, associated tags, etc.)
- **imagesvc** &ndash; Used to load images into Razor and to use those images during the (iPXE) boot process
- **tagrule** &ndash; Used to define tag rules
- **tagmatcher** &ndash; Used to define rules that will match tags to nodes
- **model** &ndash; Used to govern the process of provisioning a node with an image; includes a deployment template containing os image, answer file, and user supplied configuration data. 
- **policy** &ndash; Used to define policies that will map models to nodes based on tagrules
- [**logviewer**](/lynxbat/Razor/wiki/The%20Logviewer%20Slice) &ndash; Used to view the Razor server's current logfile
- **boot** &ndash;
- **system** &ndash;

As you can see from the output of the Razor command (shown above), it is relatively simple to use these slices from the command-line. You simply add a named slice after the razor command (and any additional switches) and you will execute the functionality of that slice.  Slices may also provide a web-based (RESTful services) interface that can be used to interact with that slice via HTTP/HTTPS. For the slices that do provide such an interface, the RESTful API will directly mimic the command-line interface (or CLI).

As an example, here is the result of running the *razor config* command via the CLI:

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
    root@ubuntu-server:~#

and here is the same command being executed through the HTTP interface on the local machine:

    root@ubuntu-server:~# wget http://127.0.0.1:8026/razor/api/config
    --2012-05-01 10:05:00--  http://127.0.0.1:8026/razor/api/config
    Connecting to 127.0.0.1:8026... connected.
    HTTP request sent, awaiting response... 200 OK
    Length: 805 [text/html]
    Saving to: `config'
    
    100%[=====================================================>] 805         --.-K/s   in 0s      
    
    2012-05-01 10:05:00 (145 MB/s) - `config' saved [805/805]
    
    root@ubuntu-server:~# cat config 
    {"@admin_port":8025,"@api_port":8026,"@default_ipmi_password":"ipmi_password","@default_ipmi_power_state":"off","@default_ipmi_username":"ipmi_user","@force_mk_uuid":"","@image_svc_host":"192.168.5.2","@image_svc_path":"/root/Razor/image","@image_svc_port":8027,"@mk_checkin_interval":60,"@mk_checkin_path":"/razor/api/node/checkin","@mk_checkin_skew":5,"@mk_fact_excl_pattern":"(^facter.*$)|(^id$)|(^kernel.*$)|(^memoryfree$)|(^operating.*$)|(^osfamily$)|(^path$)|(^ps$)|(^ruby.*$)|(^selinux$)|(^ssh.*$)|(^swap.*$)|(^timezone$)|(^uniqueid$)|(^uptime.*$)|(.*json_str$)","@mk_log_level":"Logger::DEBUG","@mk_register_path":"/razor/api/node/register","@mk_uri":"http://192.168.5.2:8026","@persist_host":"127.0.0.1","@persist_mode":"mongo","@persist_port":27017,"@persist_timeout":10,"@register_timeout":120}
    root@ubuntu-server:~# 

As you can see, the output of the second command directly mirrors the first, but the configuration is returned as a JSON-formatted hash map of containing the server configuration as name/value pairs when invoked via the RESTful interface and is returned as a simple list of name/value pairs (containing the same information) when invoked via the CLI.  This same pattern is used for all slices that provide both a CLI and a RESTful interface.