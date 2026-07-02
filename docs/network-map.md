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
- remote access overlay networking (Tailscale)
- dedicated backup network
- monitoring visibility via port mirroring

---

# Core Infrastructure IPs

| Device | IP | Purpose |
|---|---|---|
| FortiGate 50E | 10.0.0.1 | Primary edge firewall and gateway |
| Xfinity XB7 | Bridge Mode | Cable modem (bridge mode only) |
| Enterprise AP | 10.0.0.2 | Dual SSID wireless (Main + Guest) |
| Dell Proxmox VE Host | 10.0.0.46 | Primary virtualization host |
| UbuntuServer1 (VM 102) | 10.0.0.42 | Core Linux services |
| CorpDC | 10.0.0.45 | Windows Server 2025 — AD, DNS, DHCP |
| Hermes-Agent / Mara (VM 100) | 10.0.0.105 | Primary AI operations assistant |
| PBS1 | 10.0.0.30 | Local Proxmox Backup Server |
| Raspberry Pi IDS | 10.0.0.170 | Bettercap IDS / traffic monitoring |
| Managed Switch (IronGateSwitch1) | 10.0.0.254 | Core switch — VLANs, QoS, port mirroring |

---

# VLAN Design

| VLAN ID | Name | Purpose | Ports |
|---|---|---|---|
| VLAN 1 | Internal | Trusted infrastructure — all lab hosts | Ports 1–7 |
| VLAN 10 | Guest | Isolated guest wireless traffic | Port 8 |

## Switch Configuration (TP-Link TL-SG608E — IronGateSwitch1)

| Port | Assignment | Notes |
|---|---|---|
| Port 1–7 | VLAN 1 (Internal) | All lab infrastructure |
| Port 3 | Mirrored → Kali / AdminLaptop | Wireshark / passive capture |
| Port 4 | QoS High Priority | Gaming PC |
| Port 8 | VLAN 10 (Guest) | AP guest SSID uplink |

---

# Wireless Infrastructure

## Enterprise Access Point — 10.0.0.2

| SSID | VLAN | Access |
|---|---|---|
| Main (internal) | VLAN 1 | Full LAN access |
| Guest | VLAN 10 | Internet only, isolated from LAN |

---

# Dedicated Backup Network

An isolated NIC-to-NIC link carries PBS backup traffic, keeping it off the production LAN.

| Device | Interface | IP |
|---|---|---|
| Dell Proxmox VE | Backup NIC | 192.168.50.1 |
| PBS1 | Backup NIC | 192.168.50.2 |

Subnet: `192.168.50.0/24`

---

# Tailscale Mesh (Remote Access Overlay)

| Device | Tailscale IP |
|---|---|
| UbuntuServer1 | 100.100.121.22 |
| Gaming PC | 100.95.22.15 |
| Raspberry Pi IDS | 100.71.77.1 |
| CorpDC | 100.120.40.4 |
| PBS2 (offsite) | 100.98.162.11 |

Tailscale provides:
- encrypted remote management access
- offsite PBS1 → PBS2 backup replication
- admin access to CorpDC and UbuntuServer1 from anywhere

---

# DNS Infrastructure

## Internal DNS

| Provider | Host | Scope |
|---|---|---|
| CorpDC (AD DNS) | 10.0.0.45 | `corp.local` resolution, AD services |
| Pi-hole | 10.0.0.42 | DNS filtering, ad blocking, DNS analytics |

---

# IDS / Traffic Monitoring

The Raspberry Pi IDS (10.0.0.170) receives mirrored traffic from port 3 of the managed switch.

**Bettercap modules active:** `net.recon`, `net.probe`, `net.sniff`

Logs are synced to UbuntuServer1 → Grafana Alloy → Loki → Grafana dashboards.

---

# Offsite Infrastructure

| Device | Location | IP |
|---|---|---|
| PBS2 | Offsite (parents' house) | 100.98.162.11 (Tailscale) |
| OpenWrt Router | Offsite | Remote routing + Tailscale |

Future planned additions:
- Small Proxmox warm-site host for VM restoration

---

# High-Level Topology

```text
Internet
   |
Xfinity XB7 (Bridge Mode — Cable Modem)
   |
FortiGate 50E (10.0.0.1 — Primary Gateway & Firewall)
   |
TL-SG608E Managed Switch (10.0.0.254)
   |
   +---- VLAN 1 (Internal LAN 10.0.0.0/24)
   |       |
   |       +---- Dell Proxmox VE (10.0.0.46)
   |       |       +---- VM 100: Hermes-Agent/Mara (10.0.0.105)
   |       |       +---- VM 101: Windows11Edu
   |       |       +---- VM 102: UbuntuServer1 (10.0.0.42)
   |       |       +---- VM 103: Ai.Assistant
   |       |
   |       +---- CorpDC (10.0.0.45)
   |       +---- PBS1 (10.0.0.30)
   |       +---- Raspberry Pi IDS (10.0.0.170)
   |       +---- Enterprise AP (10.0.0.2)
   |
   +---- VLAN 10 (Guest — port 8)
           |
           +---- Guest wireless clients (isolated)

Backup Network (192.168.50.0/24) — isolated NIC-to-NIC
   +---- Proxmox (192.168.50.1) <---> PBS1 (192.168.50.2)

Tailscale Mesh (remote overlay)
   +---- UbuntuServer1 (100.100.121.22)
   +---- CorpDC (100.120.40.4)
   +---- Pi IDS (100.71.77.1)
   +---- Gaming PC (100.95.22.15)
   +---- PBS2 offsite (100.98.162.11)
```

---

# Design Goals

The network is designed to provide:
- realistic enterprise-style infrastructure
- VLAN segmentation and guest isolation
- centralized services
- encrypted remote access
- monitoring visibility via port mirroring and NetFlow
- isolated backup traffic
- offsite disaster recovery connectivity
