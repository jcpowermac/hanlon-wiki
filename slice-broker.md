# Broker Slice

The *broker* slice is used specify the handoff of a node to a third-party broker target. The broker slice uses a plug-in to define an instance of the desired target. The broker target is a specific instance of a broker plugin with all configuration settings specified. 

There are two types of broker plugin: agent and proxy. An agent plug-in (such as the puppet plugin) will interact directly with the node. For example, the puppet plugin will install a puppet agent on the node and connect the node to a puppetmaster for further configuration of the node. On the other hand, a proxy broker plugin (such as the ESX plugin) will point to a third party for processing and configuration. The ESX broker plugin, for example, defines the vCenter system as the target which in turn will interact with the ESX node and attach it to vCenter. 

By default `razor broker` will provide a list of all configured broker targets:

    $ razor broker
    Broker Target
       Name            Description         Plugin            Servers                      UUID
    prod_master  Production Puppet Master  puppet  [master.puppetlabs.lan]       3bqEiiwDhhChrdGeJmAEtW
    test_master  Test Puppet Master        puppet  [test_puppet.puppetlabs.lan]  4KHNmp0KxqeSfHLar7kw94


## Broker Plugin and Broker Target

The `razor broker get all|[uuid]` command will provide a list of configured brokers on the system:

    $ razor broker get all
    Broker Target
       Name            Description         Plugin            Servers                      UUID
    prod_master  Production Puppet Master  puppet  [master.puppetlabs.lan]       3bqEiiwDhhChrdGeJmAEtW
    test_master  Test Puppet Master        puppet  [test_puppet.puppetlabs.lan]  4KHNmp0KxqeSfHLar7kw94

    $ razor broker get 1bh2qyvF9eu0y8xeJMMZyM
     Name =>  prod_puppet
     Description =>  Production Puppet Server.
     Plugin =>  puppet
     Servers =>  [master.puppetlabs.lan]
     UUID =>  3bqEiiwDhhChrdGeJmAEtW

Similarly, `razor broker get plugins` provides a list of plugins available that can provide handoff of nodes to a third party.

    $ razor broker get plugins
    
    Available Broker Plugins:
     Plugin =>  puppet
     Description =>  PuppetLabs PuppetMaster

To create a new broker target, provide the plugin and target handoff server hostname.

    $ razor broker add puppet   demo_master
    [Broker] [add] <-Must Provide Broker Description [description]
    
    Command help:  razor broker (broker plugin) (Name) (Description) [(server hostname),{server hostname}]

The example below creates a new broker target named demo_master which installs puppet on the node as part of the handoff process and then connects it to the puppet master at demo_puppet.puppetlabs.lan.

    $ razor broker add puppet demo_master 'Demo Puppet Master' master.puppetlabs.lan
     Name =>  demo_master
     Description =>  Demo Puppet Master
     Plugin =>  puppet
     Servers =>  [demo_puppet.puppetlabs.lan]
     UUID =>  3bqEiiwDhhChrdGeJmAEtW

Once a broker target is created, it can be referred to in a Razor policy. The policy has the flexibility to provision different OS models combined with different puppet broker targets for system handoff in different environments. The example below shows two policies applying the same model, but specifying different broker targets for two different environments.

    $ razor policy
    Policies
    #      Label       Template            Tags           Model Label   Broker Target  Count           UUID
    1  ubuntu_prod   linux_deploy  [prod_env, vmware_vm]  ubuntu10      prod_master    1      4ikCC2fXO75swJW9NP9qdG
    2  ubuntu_test   linux_deploy  [test_env, vmware_vm]  ubuntu10      test_master    1      92jCswj8dal12jda0cz83Z

Once a broker target is no longer required, it can be removed; `remove all` will purge all broker targets specified in Razor:

    $ razor broker remove 3bqEiiwDhhChrdGeJmAEtW
    Broker remove success

    $ razor broker remove all
    Broker success
