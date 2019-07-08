---
layout: post
title:  "Benchmarking DPDK"
date:   2019-07-08 21:22:37 +0200
categories: rants dpdk linux
---

So I got to play with the Data Plane Development Kit (DPDK) from Intel recently. I planned on replicating the PVP benchmark for my use-case. I set up the following environment:

- Ubuntu 18.04 (bionic) host with:
    - KVM
    - QEMU
- Debian 10 guest images downloaded from: https://cdimage.debian.org/cdimage/openstack/current/

## Setting up (OVS+DPDK)
First of all set install OVS-dpdk using `apt`
```bash
sudo apt-get install openvswitch-switch-dpdk 
sudo update-alternatives --set ovs-vswitchd /usr/lib/openvswitch-switch-dpdk/ovs-vswitchd-dpdk
```

Restart the systemd service: `sudo systemctl restart openvswitch-switch.service`

### Isolate some CPUs
Edit the value of `GRUB_CMDLINE_LINUX_DEFAULT` to:
```ini
GRUB_CMDLINE_LINUX_DEFAULT="default_hugepagesz=1G hugepagesz=1G hugepages=4 hugepagesz=2M hugepages=2048 iommu=pt intel_iommu=on isolcpus=0,1"
```
