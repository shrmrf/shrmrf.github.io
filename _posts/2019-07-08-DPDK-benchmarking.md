---
layout: post
title:  "Benchmarking DPDK"
date:   2019-07-08 21:22:37 +0200
categories: rants dpdk linux
---
_My reference: https://software.intel.com/en-us/articles/set-up-open-vswitch-with-dpdk-on-ubuntu-server_

So I got to play with the Data Plane Development Kit (DPDK) from Intel recently. I planned on replicating the PVP benchmark for my use-case. I set up the following environment:

- Ubuntu 18.04 (bionic) host with:
    - KVM
    - QEMU
- Debian 10 guest images downloaded from: https://cdimage.debian.org/cdimage/openstack/current/

A useful tool is `numactl` (use `apt` to install it). On my non-server system, I don't have multiple socks and multiple cores per sock etc.
```console
root@machine:~# numactl  -s
policy: default
preferred node: current
physcpubind: 2 3 4 5 6 7 
cpubind: 0 
nodebind: 0 
membind: 0 
```
## Setting up (OVS+DPDK)
First of all set install OVS-dpdk using `apt`
```bash
sudo apt-get install openvswitch-switch-dpdk 
sudo update-alternatives --set ovs-vswitchd /usr/lib/openvswitch-switch-dpdk/ovs-vswitchd-dpdk
```

Restart the systemd service: `sudo systemctl restart openvswitch-switch.service`

Hugepages will need to be set up. But we need to tell DPDK about that.
```console
root@machine:~# cat /etc/dpdk/dpdk.conf
   ...
NR_1G_PAGES=4
   ...
```

### Isolate some CPUs
Edit the value of `GRUB_CMDLINE_LINUX_DEFAULT` to:
```ini
GRUB_CMDLINE_LINUX_DEFAULT="default_hugepagesz=1G hugepagesz=1G hugepages=4 hugepagesz=2M hugepages=2048 iommu=pt intel_iommu=on isolcpus=0,1"
```

And run `sudo update-grub`.

_this setting will not need to change afterwards. A reboot of the machine is required. See if the parameters got passed correctly by doing `cat /proc/cmdline`_


### Set up Hugepages
```bash
sudo mkdir -p /mnt/huge
sudo mkdir -p /mnt/huge_2mb
sudo mount -t hugetlbfs none /mnt/huge
sudo mount -t hugetlbfs none /mnt/huge_2mb -o pagesize=2MB
sudo mount -t hugetlbfs none /dev/hugepages
```

```console
root@machine:~# grep HugePages_ /proc/meminfo 
HugePages_Total:       4
HugePages_Free:        1
HugePages_Rsvd:        0
HugePages_Surp:        0
```

## Configure OVS-DPDK
The database needs to be set up only once (`sudo ovs-vsctl --no-wait init`)

```bash
sudo ovs-vsctl --no-wait set Open_vSwitch . other_config:dpdk-lcore-mask=0x03  # hopefully: cores, 0,1 
sudo ovs-vsctl --no-wait set Open_vSwitch . other_config:dpdk-socket-mem="1024"
sudo ovs-vsctl set Open_vSwitch . other_config:pmd-cpu-mask=0x0C # we have just one NUMA node
```

### Create OVS-DPDK Bridge and Ports
`dpdkvhostuser` is a predefined type. `vhost-user` is understood by Qemu 2.2 and above.
```bash
sudo ovs-vsctl add-br br0 -- set bridge br0 datapath_type=netdev
sudo ovs-vsctl add-port br0 vhost-user1 -- set Interface vhost-user1 type=dpdkvhostuser
sudo ovs-vsctl add-port br0 vhost-user2 -- set Interface vhost-user2 type=dpdkvhostuser
```

```console
root@machine:~# ovs-vsctl show
c3459670-39b8-43f7-bc57-569672ed79c6
    Bridge "br0"
        Port "br0"
            Interface "br0"
                type: internal
        Port "vhost-user2"
            Interface "vhost-user2"
                type: dpdkvhostuser
        Port "vhost-user1"
            Interface "vhost-user1"
                type: dpdkvhostuser
    ovs_version: "2.9.2"
```

_To be continued_