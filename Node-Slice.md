# Node Slice

The *node* slice is used to view the servers managed by Razor. Systems with recent check in below the mk_checkin_interval are running the microkernel, while nodes with a long check in time are running an active model which is bound to the system and are currently not actively checking into razor.

    $ razor node
    Discovered Nodes
        UUID      Last Checkin                          Tags
    000C291BD3B1  42256.6 min   [cpus_1,IntelCorporation,memsize_1,nics_1,vmware_vm]
    000C29C952BA  20s           [cpus_1,IntelCorporation,memsize_1,nics_1,vmware_vm]

Razor provides the node UUID, last system check in time and a list of tags which are associated with the system. To obtain more information about the node, specify the system UUID:

    $ razor node get 63XAZ7brS9gXUoL6PSz2wk
    UUID =>  63XAZ7brS9gXUoL6PSz2wk
    Last Checkin =>  05-09-12 16:44:13
    Tags =>  [cpus_2,IntelCorporation,memsize_2,nics_1,vmware_vm]
    Hardware IDs =>  [000C29C952BA]

For additional metadata including facter data collected from the node specify:

    $ razor node get attrib 63XAZ7brS9gXUoL6PSz2wk
    Node Attributes:
                Name                        Value              
      mk_hw_nic_count           1                              
      mk_hw_cpu0_version        6.7.10                         
      mk_hw_fw_date             06/02/2011                     
      memorytotal               1.97 GB                        
      mk_hw_lscpu_Vendor_ID     GenuineIntel                   
      mk_hw_cpu1_slot           CPU socket #1                  
      mk_hw_bus_physical_id     0                              
      virtual                   vmware                         
      mk_hw_cpu0_description    CPU                            
      mk_hw_nic0_width          64 bits                        
      mk_hw_cpu1_vendor         Intel Corp.                    
      domain                    localdomain                    
      mk_hw_disk0_physical_id   0.0.0                          
      hardwaremodel             i686                           
      network_lo                127.0.0.0                      
      fqdn                      mk000C29C952BA.localdomain     
      mk_hw_bus_description     Motherboard                    
      mk_hw_nic0_logical_name   eth0                           
      mk_hw_cpu0_capacity       4230MHz                        
      netmask_eth0              255.255.255.0                  
      mk_hw_mem_physical_id     e2                             
      mk_hw_lscpu_CPU_MHz       2654.521                       
      mk_hw_cpu0_bus_info       cpu@0                          
      macaddress_dummy0         86:A8:D2:81:26:65              
      ipaddress                 192.168.232.135                
      mk_hw_fw_version          6.00                           
      processorcount            2                              
      mk_hw_lscpu_CPU_sockets   2                              
      mk_hw_cpu1_serial         0001-067A-0000-0000-0000-0000  
      mk_hw_bus_serial          None                           
      is_virtual                true                           
      mk_hw_cpu_count           2                              
      mk_hw_nic0_capacity       1Gbit/s                        
      mk_hw_lscpu_L2_cache      3072K                          
      netmask_lo                255.0.0.0                      
      mk_hw_disk0_description   SCSI Disk                      
      network_eth0              192.168.232.0                  
      mk_hw_nic0_bus_info       pci@0000:02:01.0               
      mk_hw_cpu0_size           2667MHz                        
      mk_hw_lscpu_Stepping      10                             
      macaddress_eth0           00:0C:29:C9:52:BA              
      mk_hw_mem_description     System Memory                  
      mk_hw_cpu1_width          64 bits                        
      mk_hw_cpu0_physical_id    4                              
      mk_hw_fw_physical_id      0                              
      mk_hw_lscpu_Byte_Order    Little Endian                  
      mk_hw_cpu1_version        6.7.10                         
      mk_hw_bus_version         None                           
      mk_hw_disk0_size          40GiB (42GB)                   
      mk_hw_nic0_size           1Gbit/s                        
      mk_hw_cpu1_description    CPU                            
      mk_hw_lscpu_L1i_cache     32K                            
      mk_hw_disk_count          1                              
      architecture              i386                           
      mk_hw_nic0_physical_id    1                              
      mk_hw_cpu0_slot           CPU socket #0                  
      mk_hw_lscpu_Model         23                             
      netmask                   255.255.255.0                  
      mk_hw_cpu1_capacity       4230MHz                        
      mk_hw_cpu0_vendor         Intel Corp.                    
      mk_hw_fw_vendor           Phoenix Technologies LTD       
      mk_hw_cpu1_bus_info       cpu@1                          
      mk_hw_disk0_logical_name  /dev/sda                       
      hardwareisa               unknown                        
      mk_hw_bus_vendor          Intel Corporation              
      mk_hw_nic0_serial         00:0c:29:c9:52:ba              
      mk_hw_mem_size            2GiB                           
      mk_hw_lscpu_L1d_cache     32K                            
      mk_hw_nic0_description    Ethernet interface             
      mk_hw_cpu0_serial         0001-067A-0000-0000-0000-0000  
      mk_hw_lscpu_CPU_family    6                              
      physicalprocessorcount    2                              
      ipaddress_eth0            192.168.232.135                
      macaddress                86:A8:D2:81:26:65              
      mk_hw_fw_size             87KiB                          
      mk_hw_cpu1_size           2667MHz                        
      mk_hw_lscpu_Architecture  i686                           
      interfaces                dummy0,eth0,lo                 
      mk_hw_fw_description      BIOS                           
      memorysize                1.97 GB                        
      mk_hw_lscpu_CPU_op-modes  32-bit, 64-bit                 
      mk_hw_nic0_clock          66MHz                          
      mk_hw_cpu1_physical_id    5                              
      mk_hw_disk0_bus_info      scsi@2:0.0.0                   
      hostname                  mk000C29C952BA                 
      mk_hw_nic0_version        01                             
      mk_hw_cpu0_width          64 bits                        
      mk_hw_mem_slot            System board or motherboard    
      mk_hw_lscpu_BogoMIPS      5311.25                        
      ipaddress_lo              127.0.0.1                      

The metadata provide here can be used in tag rules or tag matches to identify nodes.
