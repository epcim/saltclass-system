
=====================
Generic system models
=====================

This repo contains general reclass system level of salt model. It is to be
used along with service models and concrete cluster deployment model.

Network configuration
=====================

Enable SR-IOV support
---------------------

Include class at `cluster.<name>.openstack.compute`

- system.nova.compute.sriov

and parameters:

- sriov_nic01: Name of the interface
- sriov_nic01_numvfs: Number of Virtual Functions
- sriov_nic01_physical_network: Default "physnet1", label for physical network interface belongs to.
- sriov_unsafe_interrupts: Default False, needs to be set True if your hw platform does not support interrupt remapping
