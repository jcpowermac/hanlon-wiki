# Config Slice

The *config* slice is used to view the current Razor server configuration.

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

If the configuration file does not exists, razor will generate a default configuration during intial startup. The configuration settings are stored in razor installation directory conf/razor_server.conf.

## Settings

The razor configuration supports the following settings:

* admin_port: razor admin management port.
* api_port: razor slice rest api port.
* default_ipmi_power_state: bmc ipmi default power state.
* default_ipmi_username: bmc ipmi login username.
* default_ipmi_password: bmc ipmi login password.
* image_svc_host: razor image service server ip.
* image_svc_port: razor image service server port.
* image_svc_path: image service ISO storage path.
* mk_checkin_interval: microkernel checkin frequency in seconds.
* mk_checkin_skew: microkernel checkin splay time in seconds.
* mk_uri: microkernel checkin ipaddress and port.
* mk_register_path: microkernel registration api url.
* mk_checkin_path: microkernel checkin api url.
* mk_fact_excl_pattern: microkernel facts excluded from meta-data collection.
* mk_log_level: microkernel log level.
* persist_mode: backend database, currently uses mongodb.
* persist_host: database host.
* persist_port: database port.
* persist_timeout: database timeout.
* register_timeout: registration timeout.
* force_mk_uuid: 
