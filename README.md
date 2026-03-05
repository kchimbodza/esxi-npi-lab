# ESXi Homelab — VM Provisioning & Network Setup

A hands-on lab demonstrating ESXi hypervisor deployment, virtual machine provisioning, and network configuration — skills directly applicable to NPI lab environments.

---

## Environment

| Component | Details |
|---|---|
| Hypervisor | VMware ESXi 8.0 Update 3 |
| Host CPU | AMD Ryzen AI 9 365 |
| Host RAM | 8 GB allocated |
| Host IP | 192.168.10.41 (bridged to LAN) |
| Datastore 1 | datastore1 — VMFS6, 13.75GB (ESXi system partition) |
| Datastore 2 | datastore2 — VMFS6, 100GB (VM storage — secondary virtual disk) |
| Host OS | Nobara Linux (VMware Workstation) |

---

## What Was Built

### 1. ESXi Hypervisor Setup
- Deployed ESXi 8.0 U3 as a nested VM on VMware Workstation
- Configured network adapter in **bridged mode** to expose ESXi directly on the local LAN (192.168.10.0/24)
- Verified management network reachability via browser at `https://192.168.10.41`

#### Why Bridged Mode on Linux (not NAT)

On Windows, VMware's NAT engine runs as a privileged system service with deep kernel integration, allowing nested hypervisors like ESXi to negotiate through the NAT layer cleanly. On Linux, the NAT service (`vmnet-natd`) is more constrained by the kernel's network namespace handling — when ESXi boots inside a VM, it attempts to take control of its own network stack, which conflicts with VMware Workstation's NAT on Linux. This causes ESXi to fall back to a 172.x.x.x address that is unreachable from the host browser.

Switching to **bridged mode** bypasses this conflict entirely — VMware binds directly to the physical NIC (via `vmnet0`), placing ESXi on the same LAN subnet as the host. This gives ESXi a real 192.168.10.x address reachable from any device on the network, which is also the correct configuration for a lab environment where multiple devices need access to the hypervisor.

### 2. VM Provisioning
Two VMs were provisioned and installed in parallel on datastore2 (100GB secondary disk):

**RHEL 10.1 (Coughlan)**
- 2 vCPUs, 2GB RAM, 10GB disk
- Guest OS: Red Hat Enterprise Linux 10.1
- Kernel: Linux 6.12.0-124.8.1.el10
- Windowing: Wayland, GNOME

**Windows Server 2022 (Standard Evaluation — Desktop Experience)**
- 2 vCPUs, 4GB RAM, 54GB disk
- Guest OS: Microsoft Windows Server 2022
- Account: Kudzayi Chimbodza
- VMware Tools installed

### 3. Network Verification
- Guest VM obtained DHCP lease from local router
- Verified connectivity: `ping 192.168.10.1` (gateway) and `ping 8.8.8.8` (external)
- Confirmed DNS resolution working

---

## Screenshots

| # | Screenshot | Description |
|---|---|---|
| 1 | `screenshots/01-esxi-console-boot.png` | ESXi 8.0 U3 boot screen in VMware Workstation — IP assigned, 8GB RAM, Ryzen AI 9 365 |
| 2 | `screenshots/02-esxi-web-dashboard.png` | ESXi web UI dashboard logged in as kudzayi — hardware, storage, and networking summary |
| 3 | `screenshots/03-dual-vm-parallel-install.png` | Windows Server 2022 and RHEL 10.1 installing simultaneously — ESXi dashboard visible in background, CPU and memory under real load |
| 4 | `screenshots/04-both-vms-running.png` | Both VMs fully booted — Windows Server 2022 showing Kudzayi Chimbodza account, RHEL 10.1 (Coughlan) showing Linux 6.12 kernel and VMware virtualization confirmed |

---

## Key Concepts Demonstrated

- **Hypervisor deployment** — ESXi installation, management network config, web UI access
- **VM lifecycle management** — creating, configuring, and powering on VMs via ESXi
- **Network topology** — bridged vs NAT mode, port groups, virtual switches (vSwitch)
- **IP addressing** — DHCP lease acquisition, gateway connectivity, DNS resolution
- **CIDR / subnetting** — host on /24 subnet (192.168.10.0/24), correct gateway config

---

## Skills Mapped to industry standards

| Industry Requirement | Demonstrated Here |
|---|---|
| Build VMs using ESXi hypervisor | ✅ VM provisioned on ESXi 8.0 U3 |
| Set up and maintain network topologies | ✅ Bridged network, vSwitch, VM Network port group |
| Maintain Linux operating systems | ✅ Linux guest VM deployed and configured |
| IP address / DNS / CIDR notation | ✅ DHCP, ping, DNS verification on 192.168.10.0/24 |
| Attention to detail | ✅ Documented each step with screenshots |

---

## Related Homelab Work

This lab is part of a broader homelab documented at [myviewsontech.com](https://myviewsontech.com), which includes:

- MQTT broker with TLS (Mosquitto)
- Alta Labs managed networking (R10 router, AP6 access point)
- ZimaOS NAS with 4x NVMe RAID 5
