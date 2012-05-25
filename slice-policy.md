# Policy Slice

The `policy` slice is used to create policy rules that bind nodes to specific models based on tags that describe the nodes. Multiple policies are filtered top down in a way similar to firewall rules where the first suitable applicable policy will be applied. Generic policy should be specified last, while specific policy should be specified first.


## Managing Policy

By default, `razor policy` will provide a list of active policies:

    $ razor policy
    Policies
    #  Enabled      Label          Tags        Model Label    Count           UUID
    0  false    opensuse        [vmware_vm]  install_suse     5      7GxyQCWGHEStPQWzx5Nhik
    1  true     centos          [vmware_vm]  install_centos   8      3FlZM8JSeJBjETgQxowAp8
    2  false    install_esx     [physical]   install_esx      0      1qF8J23HVkUm6BYOa7Qhg2
    3  false    precise         [vmware_vm]  install_precise  0      2Of6sqhrX96KIlpDKsvUF8
    4  false    esx             [vmware_vm]  install_esx      0      4GgYNsVOUTiBe2JboPMQHK
    5  false    centos_puppet   [vmware_vm]  install_centos   1      1Qw9ML1ZKilSWFwHOa8vWM
    6  true     precise_puppet  [vmware_vm]  install_precise  1      4Sj91LzXLbTjSYP73NlFWc

The active policy above describes ubuntu deployed on a vm with a single cpu, linux on a vm with four cpu's and a puppet broker handoff, and esx deployed on physical hardware. (Support for reordering rules will be added in a future release.)

### Adding Policy

Razor policies are associated with a policy template. A list of available policy templates can be obtained via the following command:

    $ razor policy get template
    
    Policy Templates:
        Template                                                  Description
    linux_deploy       Policy for deploying a Linux-based operating system. Compatible with Linux operating system Models
    vmware_hypervisor  Policy for deploying a VMware hypervisor. Compatible with VMware hypervisor Model Configs

Additional policy templates can be loaded into the system to extend Razor's functionality.

    $ razor policy add
    [Policy] [add_policy] <-Must Provide A Policy Template [template]
    
    Command help:
    razor model add template=(policy template) label=(policy label) model_uuid=(model UUID) broker_uuid=(broker UUID)|none tags=(tag){,(tag),(tag)..} {enabled=true|false}
       template:    The Policy Template name to use
       label:       A label to name this Policy
       model_uuid:  The Model to attach to the Policy
       broker_uuid: The Broker to attach to the Policy or 'none'
       tags:        At least one tag to trigger the Policy. Comma delimited
       enabled:     Whether the Policy is enabled or not. Optional and defaults to false

In additional to a policy template, a model uuid must be provided indicating which model to bind to the applicable nodes. If a specific uuid isn't provided, Razor will provide a list of suitable policies:

    $razor policy add linux_deploy install_linux
    [Policy] [add] <-Must Provide Model Config UUID [model_config]
    
    Command help:  razor policy add (policy templates) (Name) (Model Config UUID) [none|(Broker Target UUID)] (tag{,tag,tag})
    Valid Models for (linux_deploy)
     Label =>  ubuntu
     Template =>  linux_deploy
     Description =>  Ubuntu Oneiric 11.10 Minimal
     UUID =>  5SL6gy6V1OXzFKWOfgsqxG

Optional brokers can also be specified to trigger a node handoff to external systems after the active model is applied. For example a puppet broker plugin will install puppet on the target system and connect the node to an appropriate puppet master. If no such broker is selected, Razor will not initiate any handoff process after binding an active model to the node.

    $ razor broker
    Broker Targets:
     Name =>  puppet
     Description =>  Puppet agent handoff.
     Plugin =>  puppet
     Servers =>  [master.puppetlabs.lan]
     UUID =>  7S18suoe7ZsT9hv0ARUDW6

    $ razor policy add template=linux_deploy label="precise_puppet" model_uuid=3LCN86Cpx0Te3Of5WbORkQ broker_uuid=7S18suoe7ZsT9hv0ARUDW6 tags=vmware_vm enabled=true
    Policy created
     UUID =>  5VZtiprDnQHIFcePfMt90E
     Line Number =>  7
     Label =>  precise_puppet
     Enabled =>  true
     Template =>  linux_deploy
     Description =>  Policy for deploying a Linux-based operating system.
     Tags =>  [vmware_vm]
     Model Label =>  install_precise
     Broker Target =>  puppet
     Bound Count =>  0

The list of comma seperated tags are the criteria by which Razor identifies nodes that should have the specified policy applied to them. In the example below, a vmware node with 1 cpu will get the install_precise policy. The policy order will determine the priority in which the rule will be applied to the node. If the default tags are insufficient, tags for identifying nodes can be created via razor tags.

    $ razor policy add template=linux_deploy label="precise_puppet" model_uuid=3LCN86Cpx0Te3Of5WbORkQ broker_uuid=7S18suoe7ZsT9hv0ARUDW6 tags=cpus_1,vmware_vm enabled=true
    Policy created
     UUID =>  hacuN68lnXrlj83tNo3sQ
     Line Number =>  8
     Label =>  precise_puppet
     Enabled =>  true
     Template =>  linux_deploy
     Description =>  Policy for deploying a Linux-based operating system.
     Tags =>  [cpus_1, vmware_vm]
     Model Label =>  install_precise
     Broker Target =>  puppet
     Bound Count =>  0

To find out the current state of active models bound to nodes, use `policy active`:

    razor policy active
    Active Models:
         Label          State           Node UUID            System      Bind #           UUID
    centos_install   init         6ifxsUY43FZWuykB0tlrA2  none           1       6ph1ArWrUa2RPbBlXDRNtq
    centos_install   init         49ZAUZJmQLDyQxLd6ZhEE6  none           2       4GZwgJ4xaVvGHUW0qHjIhW
    install_precise  postinstall  7l4hBeHQOEioBMzglVrnFi  none           1       5LR9QOvX0wj47oKlt29UJm
    esx_install      postinstall  YDLLKKJ9u3RS3QEcqdwx0   puppet_master  2       fvIBp22fNXlAoeMQdyilO
    esx_install      init         2AiYujFMynjiLS4URaeoKw  puppet_master  1       2HPrdJ0ctz7A8Z5NLvzYwU

The active model can be reviewed to find out what action the node is currently performing and what state it has transitioned through to get to the current state:

    razor policy active log fvIBp22fNXlAoeMQdyilO
          State            Action      Result    Time     Last     Total           Node
    init               mk_call         n/a     14:49:32  0 sec    0 sec    YDLLKKJ9u3RS3QEcqdwx0
    init               boot_call       n/a     14:50:02  30 sec   30 sec   YDLLKKJ9u3RS3QEcqdwx0
    init               kickstart_file  n/a     14:51:59  1.9 min  2.5 min  YDLLKKJ9u3RS3QEcqdwx0
    init=>postinstall  kickstart_end   n/a     14:52:00  1 sec    2.5 min  YDLLKKJ9u3RS3QEcqdwx0
