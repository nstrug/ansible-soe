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
- **hacluster_vcentre_ip** IP address of Vmware vCenter (vmware fencing only)
- **hacluster_vcentre_login** Username to use to access the Vmware vCenter (vmware fencing only)
- **hacluster_vcentre_passwd** Password to use to access the VMware vCentre (vmware fencing only)


