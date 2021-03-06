# cluster-profile-prep and configure-cluster

Together these two playbooks are used to configure Red Hat Cluster Suite clusters.


## cluster-profile-prep

This is run against individual hosts during the deployment process as an Ansible Tower Callback.
It installs dependencies, opens firewall ports and prepares the node for integration into a Red Hat Cluster.

It does not however configure the cluster, this is done at a later stage by the **configure-cluster** playbook.

For XVM fencing, the playbook will use the vaulted key stored at ```{{ playbook_dir}}/files/{{ foreman_params.hacluster_fence_xvm_key }}```

## configure-cluster

This is run against all hosts that should be clustered together. It should be run after hosts have been deployed
and will usually be run with a limit specified. The playbook will attempt to cluster all hosts specified in the limit.

## Parameters

The playbooks require the following parameters to be set in Red Hat Satellite. 
The parameters above should be the same for all nodes in the cluster, therefore it is better to 
set them as hostgroup or host collection parameters, rather than on individual nodes.

- **hacluster_fencing** The fencing type to use. This can either be set to ```xvm``` to specify XVM fencing
  as used by libvirt, or ```vmware``` to specify VmWare fencing.
- **hacluster_passwd** The password to use for authenticating the ```pcs``` user.
- **hacluster_name** The name of the cluster to configure.
- **hacluster_fence_xvm_key** filename of fence_xvm_key to use (XVM fencing only)
- **hacluster_vcentre_ip** IP address of Vmware vCentre (vmware fencing only)
- **hacluster_vcentre_login** Username to use to access the Vmware vCentre (vmware fencing only)
- **hacluster_vcentre_passwd** Password to use to access the VMware vCentre (vmware fencing only)

**NB** The playbook expects ```hacluster_vcentre_passwd``` to be an AES256 encrypted string. You can generate this
using:

```echo -n <plaintext_passwd> | openssl enc -e -aes-256-cbc -a -salt```

and entering a passphrase when prompted. The encrypted string that is output should be stored 
in the ```hacluster_vcentre_passwd``` in satellite.

The playbook will attempt to decrypt the string with an extra_var called ```foreman_key```. This will not
be echoed to the log output, however for additional security it would make sense to store this key in
a custom credential, rather than adding it to the inventory.
  


