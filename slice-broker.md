# Broker Slice

The *broker* slice is used specify handoff of node to external broker target. There are two types of broker plugin, agent and proxy. An agent broker will interact directly with the node, such as the puppet plugin, which installs a puppet agent and connects the node to a puppetmaster. An proxy broker will interact with a third party, such as the ESX plugin, which interacts with vCenter system to attach the ESX node. The broker target is a specific instances of broker plugin with all configuration settings specified. By default razor broker will provide a list of configured broker targets:

    $ razor broker
    Broker Target
       Name            Description         Plugin            Servers                      UUID
    prod_master  Production Puppet Master  puppet  [master.puppetlabs.lan]       3bqEiiwDhhChrdGeJmAEtW
    test_master  Test Puppet Master        puppet  [test_puppet.puppetlabs.lan]  4KHNmp0KxqeSfHLar7kw94


## Broker Plugin and Broker Target

razor broker get plugins provide a list of plugins available that provides handoff of node to with a third party.

    $ razor broker get plugins
    
    Possible Broker Plugins:
     Plugin =>  puppet
     Description =>  PuppetLabs PuppetMaster

To specify a new broker target, provide the plugin and target handoff server hostname.

    $ razor broker add puppet   demo_master
    [Broker] [add] <-Must Provide Broker Description [description]
    
    Command help:  razor broker (broker plugin) (Name) (Description) [(server hostname),{server hostname}]

The example below creates a new broker target named demo_master which installs puppet on the node as part of the handoff process and connects to the server demo_puppet.puppetlabs.lan.

    $ razor broker add puppet demo_master 'Demo Puppet Master' master.puppetlabs.lan
     Name =>  demo_master
     Description =>  Demo Puppet Master
     Plugin =>  puppet
     Servers =>  [demo_puppet.puppetlabs.lan]
     UUID =>  3bqEiiwDhhChrdGeJmAEtW

Once a broker target is no longer required in razor, it can be removed, and remove all will purge all broker target specified in razor:

   $ razor broker remove 3bqEiiwDhhChrdGeJmAEtW
   Broker remove success

   $ razor broker remove all
   Broker success

Once a broker target is created, it can be specified in a razor policy. The policy have the flexibility to specify different OS model for provisioning in combinations with different puppet broker target for system handoff for different environments. The example below shows two policy applying the same model, but specifying different broker target for two different environments.

    $ razor policy
    Policies
    #      Label       Template            Tags           Model Label   Broker Target  Count           UUID
    1  ubuntu_prod   linux_deploy  [prod_env, vmware_vm]  ubuntu10      prod_master    1      4ikCC2fXO75swJW9NP9qdG
    2  ubuntu_test   linux_deploy  [test_env, vmware_vm]  ubuntu10      test_master    1      92jCswj8dal12jda0cz83Z
