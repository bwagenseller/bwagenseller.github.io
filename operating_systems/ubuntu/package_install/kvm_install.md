# KVM Overview
<!-- clear for public use --> 

> This doc outlines how to install kvm on a basic Ubuntu server; to set up a basic Ubuntu server please [see these instructions](operating_systems/ubuntu/server_build), and for general Ubuntu Linux operation [please see these instructions](operating_systems/ubuntu/linux_notes). When setting up the Ubuntu server, make sure to [set up bridging](operating_systems/ubuntu/server_build?id=bridging).

KVM (Kernel-based Virtual Machine) is a package suite that turns your host into a 'hypervisor', which has the ability to host virtual machines that can act as if they were independent servers. This is a very powerful concept, as it means you can easily make a new entity that looks, acts, and is a new server (for the most part). The only limitation is the `host` / `hypervisor` (which basically means 'the operating system on the actual physical server which controls child virtual machines') itself: CPUs and memory / RAM are limited on the host, so you can only divvy out so much before the system can no longer support more virtual machines (`VMs`).

> When discussing hosts and VMs, it can get confusing as sometimes its ambiguous if the instructions mean 'do this on the host' or 'do this inside the VM'. For these install instructions, **everything** is done on the host.

# Credits

This is based on the instructions first laid out by a Packers fan who agreed to allowing me to share this but did not want to be named. The Packers fan is highly skilled at Linux (and IT related fields in general), and I am in his debt for all of the knowledge I have gained from him over the years. I salute you, good sir, and thank you for everything!

# Check Host Requirements

We must first make sure the host can even run KVM (or more specifically, if it can handle virtualization).  Read on this [here](http://www.linux-kvm.org/page/FAQ#What_do_I_need_to_use_KVM.3F), [here](http://manpages.ubuntu.com/manpages/zesty/man1/kvm-ok.1.html), and [here](https://yoyoclouds.wordpress.com/2012/04/25/how-to-check-whether-your-pc-has-virtualization-enabled-or-not/).

# Install Remote Access Packages

We are going to install a few things that will allow us to remotely launch a desktop that will run on this machine. To do so, install these packages on the server:
```
apt-get install xfce4 xfce4-goodies
apt-get install xrdp xorgxrdp
```
* `xfce4` is a light Desktop application users can use to interact with the KVM GUI remotely.
* `xrdp` allows remote desktop connection (this is needed to connect to xfce4 remotely).

You will also have to make sure xrdp is running; to do so, run:
```
service xrdp status
```

If it is installed but inactive / not running, start it with:
```
service xrdp start
```

---

# KVM Basic Install Steps

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root).

2\. [Update all packages](/operating_systems/ubuntu/linux_notes?id=updating-upgrading-all-packages).

3\. Install the following packages:
```
apt-get install qemu-kvm
apt-get install libvirt-bin
apt-get install ubuntu-vm-builder
apt-get install bridge-utils
apt-get install virt-manager
apt-get install virtinst
apt-get install uuid-runtime
```

Or, alternatively:
```
apt-get install qemu-kvm libvirt-bin ubuntu-vm-builder bridge-utils virt-manager virtinst uuid-runtime
```

What we installed:
* qemu-kvm is KVM.
* libvirt-bin and ubuntu-vm-builder are support packages for KVM.
* virtinst will allow us to mount an ISO to a blank VM (no OS).
* bridge-utils is for bridging (as necessary).
* virt-manager is a GUI to manage virtual machines.
* uuid-runtime contains the command `uuidgen` which can generate random UUIDs.
---

# Removing the Screensaver

To remove the screen saver, run the removal then remove ancillary files:
```
apt-get -y remove xscreensaver
rm /usr/lib/xscreensaver/*
```

---

# Adding User to Group 'libvirt'

!> Note that some other installations use the group 'libvirtd' instead!

We must add all users that we want to be able to control KVM to the group 'libvirt'. Run this for all users (replace `uperson` with their login):
```
usermod -a -G libvirt uperson
```

!> People can still log into the VM as they would any other VM without being in the group 'libvirt'; this is _purely_ for managing the _overall_ VM in the KVM GUI.

---

# Checking Install Thus Far

To check if KVM is running and everything is installed correctly, run:
```
virsh list --all
```

> Alternatively, you can run `virsh -c qemu:///system list`


If you see this you are good to go:
>  Id    Name                           State <br>
> ----------------------------------------------------  <br>
> 

If you see anything else, try following the instructions at the [KVM install guide on Ubuntu.com](https://help.ubuntu.com/community/KVM/Installation).

---

# Bonding For KVM

Bonding isn't entirely necessary for KVM, although if this is a large server and there are multiple Ethernet connections on it, its a good idea to bond them.

If you need to set up bonding, [please see these instructions](operating_systems/ubuntu/server_build?id=bonding). To make a bridge on Ubuntu 16.04 and below, [please follow this link](operating_systems/ubuntu/server_build?id=bonding-ubuntu-1604-and-before), and if you are on Ubuntu 17.10 or later, [please follow this link](operating_systems/ubuntu/server_build?id=bonding-ubuntu-1710-and-after).

---

# Bridging For KVM

There needs to be some way for a VM to get its own IP address from your router.  Natively, a VM is connected to a NAT, which allows the VMs to use the host's internet capabilities and also allows the VMs and the host to communicate with each other using SSH; any different host that is under the same router will not be able to see the VMs, though, as while using NAT the IP assigned to the VM is specific to the host only (its IP is meaningless outside the host). There needs to be a way the VM can directly interface with the router to negotiate for an IP; the way this is done is through the concept of `bridging`, and is critical of you want your VMs to communicate with other nodes on the router.

If you need to set up bridging, [please see these instructions](operating_systems/ubuntu/server_build?id=bridging). To make a bridge on Ubuntu 16.04 and below, [please follow this link](operating_systems/ubuntu/server_build?id=bridging-ubuntu-1604-and-before), and if you are on Ubuntu 17.10 or later, [please follow this link](operating_systems/ubuntu/server_build?id=bridging-ubuntu-1710-and-after).

---

# Virtual Machine Storage

If you would like to store all VM images in one volume/partition, you can follow these instructions to set that up.

To define the storage pool directory, we can run:
```
virsh pool-define-as VirtualMachines dir - - - - "/data/VirtualMachines"
```
* We defined our pool as 'VirtualMachines'
* Our base directory we will use for our VMs is '/data/VirtualMachines'

Check to see if we entered the pool correctly:
```
virsh pool-list --all
```

We should see something like
> Name                 State      Autostart <br>
> ------------------------------------------- <br>
> VirtualMachines      inactive   no


Now, build/register the storage pool, activate the storage volume, and set the autostart to 'yes':
```
virsh pool-build VirtualMachines
virsh pool-start VirtualMachines
virsh pool-autostart VirtualMachines
```

Confirm the state is active and the autostart is 'yes' by re-checking the pool; run `virsh pool-list --all` and you should see the following:

> Name                 State      Autostart <br>
> ------------------------------------------- <br>
> VirtualMachines      active     yes


If you wish to check the pool info in the future you can run:
```
virsh pool-info VirtualMachines
```

# Ancillary Tools

These tools are not required to run KVM, but they are helpful in various ways.

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root).

2\. [Update all packages](/operating_systems/ubuntu/linux_notes?id=updating-upgrading-all-packages).

3\. Install the following packages:
```
apt-get install libguestfs-tools
apt install python-guestfs
apt install virt-top
```

Or, alternatively:
```
apt-get install libguestfs-tools python-guestfs virt-top
```

What we installed:
* `libguestfs-tools` / `python-guestfs` is for [libguestfs](operating_systems/ubuntu/package_operations/kvm_notes?id=libguestfs-tools), and its used to mount image filesystems directly to the host's filesystem.
* `virt-top` is for [Virtual-top](operating_systems/ubuntu/package_operations/kvm_notes?id=virtual-top), which is similar to htop, but for KVM.


