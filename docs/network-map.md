# Network Map

## Core Network

Primary LAN subnet:

```text
10.0.0.0/24
```

The environment uses a hybrid network design combining:
- wired infrastructure
- VLAN segmentation
- enterprise wireless
- remote access overlay networking
- monitoring visibility

---

# Core Infrastructure IPs

| Device | IP | Purpose |
|---|---|---|
| Xfinity Gateway | 10.0.0.1 | Internet gateway/router |
| Managed Switch | 10.0.0.254 | Core switching/VLANs |
| Proxmox VE Host | 10.0.0.46 | Virtualization host |
| UbuntuServer1 | 10.0.0.42 | Linux services VM |
| CorpDC | 10.0.0.45 | Windows AD/DNS/DHCP |
| PBS1 | 10.0.0.30 | Local backup server |
| Raspberry Pi IDS | 10.0.0.170 | IDS / Bettercap |
| Gaming PC | 10.0.0.152 | Primary desktop |
| Laptop | 10.0.0.213 | Administrative workstation |

---

# Wireless Infrastructure

## Enterprise Access Point

The lab uses an enterprise-grade AP providing:
- primary internal SSID
- isolated guest wireless network
- VLAN-backed guest access

This allows:
- guest device isolation
- internal resource protection
- realistic enterprise wireless segmentation

---

# VLAN Design

The environment uses VLAN segmentation for:
- guest wireless traffic
- management separation
- monitoring visibility
- future security expansion

## Current VLAN Concepts

| VLAN Purpose | Description |
|---|---|
| Main LAN | Trusted internal systems |
| Guest VLAN | Isolated guest wireless traffic |
| Management Network | Infrastructure administration |
| Monitoring Visibility | IDS and mirrored traffic |

---

# IDS / Traffic Monitoring

The Raspberry Pi IDS receives mirrored traffic from the managed switch.

## IDS Features

- Bettercap-based monitoring
- traffic inspection
- network visibility
- security experimentation
- DNS monitoring
- traffic analysis

## Switch Features Used

- Port mirroring
- VLAN support
- QoS

---

# Remote Access Architecture

Remote access uses:

```text
Tailscale
```

This provides:
- encrypted remote connectivity
- offsite PBS communication
- remote management access
- disaster recovery access

---

# Offsite Infrastructure

## Parents House Infrastructure

Current offsite systems:
- OpenWrt router
- PBS2
- Tailscale connectivity

Future planned additions:
- Proxmox warm-site host
- restore-ready disaster recovery environment

---

# Backup Network

A dedicated backup network exists between:
- Proxmox VE host
- PBS1

## Backup Subnet

| Device | IP |
|---|---|
| Proxmox Backup NIC | 192.168.50.1 |
| PBS1 Backup NIC | 192.168.50.2 |

Purpose:
- isolated backup traffic
- improved backup performance
- reduced LAN congestion

---

# DNS Infrastructure

## Internal DNS

Provided by:
- CorpDC (Windows Server 2025)

Functions:
- Active Directory DNS
- internal hostname resolution
- domain services

## DNS Filtering

Additional DNS visibility/filtering provided by:
- Pi-hole

---

# High-Level Topology

```text
Internet
   |
Xfinity Gateway
   |
Managed Switch
   |
+----------------------------+
| Main LAN / VLANs           |
+----------------------------+
   |
   +---- Proxmox VE Host
   |       +---- UbuntuServer1
   |       +---- AI Assistant VM
   |
   +---- CorpDC
   |
   +---- PBS1
   |
   +---- Enterprise AP
   |
   +---- Raspberry Pi IDS
   |
   +---- Pi-hole

PBS1
   |
Tailscale / Remote Connectivity
   |
PBS2 (Offsite)
```

---

# Design Goals

The network is designed to provide:
- realistic enterprise-style infrastructure
- segmentation practice
- centralized services
- remote access
- monitoring visibility
- backup reliability
- disaster recovery capability
- operational flexibility
