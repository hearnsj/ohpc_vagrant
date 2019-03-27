Project to provision an [OpenHPC](https://openhpc.community/) + [Open OnDemand](https://openondemand.org/) cluster via Vagrant using the
CRI_XCBC (XSEDE basic cluster) Ansible provisioning framework.

The Vagrantfile takes inspiration from the [vagrantcluster](https://github.com/cluening/vagrantcluster)
project but is oriented toward deploying only a master node
and using standard OHPC tools to provision the cluster, and
therfore favors the CRI_XCBC approach to ansible scripts just
for the master.

The Vagrantfile is stripped to the core (rather that carry all
the cruft of a vagrant init).  It leverages work from a
[pilot project](https://gitlab.rc.uab.edu/ravi89/ohpc_vagrant)
(primaryly the development of an updated centos 7.5 image)
but prefers a clean repo slate.

## Project Setup

Clone this project recursively to get the correct version for the
CRI_XSEDE submodule to build the OpenHPC(ohpc) and Open OnDemand (ood) nodes
```
git clone --recursive https://gitlab.rc.uab.edu/jpr/ohpc_vagrant.git
```

## Cluster Setup

After setting up the project above create your single node OpenHPC
cluster with vagrant:
```
vagrant up ohpc
```

The ansible config will bring the master node to the point where its
ready to ingest compute nodes via wwnodescan and prompt to you
start a compute node.  You can create a compute node and start it with
the helper scripts:

Create node c0 (choose whatever name makes sense, c0 matches the config):
```
compute_create c0
```

When prompted start compute node c0:
```
compute_start c0
```

If you want to stop the compute node:
```
compute_stop c0
```

If you want to get rid of the compute node VM:
```
compute_destroy c0
```

Note, the compute scripts work directly with the VirtualBox hypervisor.  The
machine created is a basic, lightweight diskless compute node the boots
via iPXE from the OpenHPC master.   You may need to adjust the path to the
ipxe.iso in compute_create to match your local environment.

## Cluster Check

After the `vagrant up ohpc` completes you can can log into the cluster with `vagrant ssh ohpc`.

To confirm the system is operational run `sinfo` and you should see the following text:
```
PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST
low*         up 2-00:00:00      1   idle c0
```

You can run a test command on the compute node via slurm using:

```
srun hostname
```

This should return the name `c0`.

With these tests confirmed you have a working OpenHPC cluster running slurm.

## Boot the Open OnDemand node

A primary function of this project is to provide a dev/test cluster for working
with Open OnDemand.  After the cluster is up boot the ood node with:
```
vagrant up ood
```

This will provision the node.

NOTE: Near the end of the ood provisioning, the ansible scripts will display several
sudo commands that need to be run on the ohpc node to register the ood node
with the cluster. The commands ensure system file synchronization and slurm work. 
You will need to copy and paste these sudo commands to a shell in ohpc.  The
ansible script will pause for 90 seconds to give you time to do this.

After the node is provisioned (or booted) you need to work around a mount issue
with NFS mounts in the centos/7 vagrant box and issue the `mount -a` command 
on the ood node:
```
vagrant ssh ood -c "sudo mount -a"
```

After this point you can connect to the web ui of the ood node, typically via
(the port mapping may change in your local vagrant env):

http://localhost:8080

The default user name and password for the web UI is 'vagrant'.
