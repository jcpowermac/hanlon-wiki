1. First take a look at existing images by
    razor image

Make sure that VMware-VMvisor-Installer-5.0.0-469512.x86_64.iso is in
the list, if not, do a 
    razor image add esxi VMware-VMvisor-Installer-5.0.0-469512.x86_64.iso

2. We need a model so take a look at the model templates available:
    razor model get template

There should be one called vmware_esxi5_simple.  Build a model with it
by
    razor model add vmware_esxi5_simple (model name) {image uuid}

{image uuid} is the uuid of VMware-VMvisor-Installer-5.0.0-469512.x86_64.iso
we added in step 1.

For now, use the example AAAAA-BBBBB-CCCCC-DDDDD-EEEEE as the ESX
License Key, when asked.

3. This step is similar to the last one and creates a policy.
    razor policy get template
    razor policy add (policy templates) (Name) (Model Config UUID) [none|(Broker Target UUID)] (tag{,tag,tag})

A real-world example of the second command is
    razor policy add vmware_hypervisor esxi_policy 3sI1xHdfQbyLUunhmDGZTk none vmware_vm


