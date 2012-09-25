The `config` slice is used to view the current configuration of the Razor server.
```bash
ProjectRazor Config:
        image_svc_host: 192.168.1.1 
        persist_mode: mongo 
        persist_host: 127.0.0.1 
        persist_port: 27017 
        persist_timeout: 10 
        admin_port: 8025 
        api_port: 8026 
        image_svc_port: 8027 
        mk_tce_mirror_port: 2157 
        mk_checkin_interval: 60 
        mk_checkin_skew: 5 
        mk_uri: http://192.168.1.1:8026 
        mk_register_path: /razor/api/node/register 
        mk_checkin_path: /razor/api/node/checkin 
        mk_fact_excl_pattern: (^facter.*$)|(^id$)|(^kernel.*$)|(^memoryfree$)|(^operating.*$)|(^osfamily$)|(^path$)|(^ps$)|(^ruby.*$)|(^selinux$)|(^ssh.*$)|(^swap.*$)|(^timezone$)|(^uniqueid$)|(^uptime.*$)|(.*json_str$) 
        mk_log_level: Logger::ERROR 
        mk_tce_mirror_uri: http://localhost:2157/tinycorelinux 
        mk_tce_install_list_uri: http://localhost:2157/tinycorelinux/tce-install-list 
        mk_kmod_install_list_uri: http://localhost:2157/tinycorelinux/kmod-install-list 
        image_svc_path: /opt/Razor/image 
        register_timeout: 300 
        force_mk_uuid:  
        default_ipmi_power_state: off 
        default_ipmi_username: ipmi_user 
        default_ipmi_password: ipmi_password 
        daemon_min_cycle_time: 30 
        node_expire_timeout: 900 
        rz_mk_boot_debug_level:  
```
If the configuration file does not exist, Razor will generate a default configuration during intial startup. The configuration settings are stored in the Razor installation directory: conf/razor_server.conf.

## Settings

The Razor configuration slice reports on the following settings:

* admin_port: razor admin management port.
* api_port: razor slice RESTful api port.
* daemon_min_cycle_time: the minimum cycle time for the razor daemon (in seconds).
* default_ipmi_username: username for the node BMCs.
* default_ipmi_password: password for the node BMCs.
* default_ipmi_power_state: default power state for the node BMCs.
* image_svc_host: razor image service IP address.
* image_svc_port: razor image service port.
* image_svc_path: local image service ISO storage path.
* mk_uri: URI to use for microkernel checkin/register requests.
* mk_checkin_path: path to use for microkernel checkin requests (appended to mk_uri, above).
* mk_checkin_interval: microkernel checkin interval (in seconds).
* mk_checkin_skew: maximum initial microkernel checkin splay time (in seconds).
* mk_register_path: path to use for microkernel registration requests (appended to mk_uri, above).
* mk_fact_excl_pattern: pattern defining facts that should not be excluded from all registration requests sent to the razor server.
* mk_tce_mirror_uri: mirror to use when downloading microkernel extensions.
* mk_tce_mirror_port: port to use when downloading microkernel extensions.
* mk_tce_install_list_uri: URI for the list of microkernel extensions to install during boot process.
* mk_kmod_install_list_uri: URI for the list of kernel modules to load during the boot process.
* mk_log_level: microkernel log level.
* node_expire_timeout: node expiration timeout (in seconds)
* persist_host: database host.
* persist_port: database port.
* persist_mode: backend database (currently uses mongodb).
* persist_timeout: database persistence timeout.
* register_timeout: microkernel registration timeout.
* rz_mk_boot_debug_level: microkernel debug level (for initial boot); defaults to the same value as the mk_log_level (above) if not specified.
* force_mk_uuid: (currently unused?) 

## Setting the Razor Server Debug Level

To modify the debug level, set the environment variable `RAZOR_LOG_LEVEL`.

    export RAZOR_LOG_LEVEL=3; start_node.sh

The log level value corresponds to the Ruby Logger class:

* 0: Logger::DEBUG
* 1: Logger::INFO
* 2: Logger::WARN
* 3: Logger::ERROR
* 4: Logger::FATAL
