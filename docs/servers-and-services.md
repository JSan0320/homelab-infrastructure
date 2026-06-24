# Servers and Services

## Overview

The homelab uses a hybrid infrastructure environment consisting of:

- Proxmox virtualization
- Linux infrastructure services
- Windows enterprise services
- centralized monitoring
- backup infrastructure
- security tooling
- AI-assisted operations

The environment is designed to simulate many small-enterprise infrastructure concepts.

---

# Core Infrastructure Systems

| System | IP | Purpose |
|---|---|---|
| Dell PowerEdge T320 | 10.0.0.46 | Primary Proxmox VE virtualization host |
| UbuntuServer1 (VM 102) | 10.0.0.42 | Main Linux infrastructure services VM |
| Hermes-Agent / Mara (VM 100) | 10.0.0.105 | Primary AI operations assistant |
| Ai.Assistant (VM 103) | — | Legacy Telegram bot + Ollama |
| Windows11Edu (VM 101) | — | Windows 11 admin workstation (stopped) |
| Windows Server 2025 (CorpDC) | 10.0.0.45 | Active Directory, DNS, DHCP, Group Policy |
| PBS1 | 10.0.0.30 | Local Proxmox Backup Server |
| PBS2 | Tailscale | Offsite Proxmox Backup Server |
| Raspberry Pi IDS | 10.0.0.170 | Network traffic monitoring / Bettercap |
| Pi-hole | 10.0.0.42 | DNS filtering and visibility |

---

# Proxmox VE Host

## Hardware

| Item | Details |
|---|---|
| System | Dell PowerEdge T320 |
| Role | Primary virtualization host |
| IP (LAN) | 10.0.0.46 |
| Backup NIC | 192.168.50.1 |
| Hypervisor | Proxmox VE 9.1 |

## Hosted VMs

|| VMID | Name | IP | RAM | CPU | Role |
||---|---|---|---|---|---|
|| 100 | Hermes-Agent | 10.0.0.105 | 16 GB | 4 | AI ops assistant (Mara) |
|| 101 | Windows11Edu | — | 16 GB | 4 | Admin workstation (stopped) |
|| 102 | Ubuntu.Server | 10.0.0.42 | 48 GB | 6 | Core Linux services |
|| 103 | Ai.Assistant | — | 16 GB | 4 | Legacy Telegram bot + Ollama |

---

# UbuntuServer1 (VM 102)

## System Information

| Item | Details |
|---|---|
| VMID | 102 |
| IP | 10.0.0.42 |
| RAM | 48 GB |
| CPU | 6 cores |
| Role | Primary Linux services VM |
| Platform | Ubuntu Server |

## Main Services

| Service | Purpose | Port |
|---|---|---|
| Samba | Network file shares | 445 |
| Grafana | Monitoring dashboards | 3000 |
| Prometheus | Metrics collection | 9090 |
| Loki | Centralized log aggregation | 3100 |
| Grafana Alloy | Log/metric pipeline (NetFlow, IDS) | — |
| Wazuh | SIEM / security monitoring | 55000 (API), 443 (dashboard) |
| Jellyfin | Media streaming | 8096 |
| Plex | Media streaming | 32400 |
| goflow2 | NetFlow collector | 2055 (flow), 8080 (metrics) |
| Webmin | Web-based administration | 10000 |
| Homarr | Homelab dashboard | 7575 |
| Pi-hole | DNS filtering / ad blocking | 53 |

## Samba Storage

| Path | Disk | Size | Purpose |
|---|---|---|---|
| `/srv/samba/FILES` | `sdd` (WD 2TB SATA) | ~167 GB used / 1.6 TB free | Production Samba share |
| `/mnt/backup` | `sdc` (WD 2TB SATA) | Mirror of FILES | Local DR backup (rsync daily at 02:00) |

---

# Hermes-Agent — VM 100 (Mara)

## System Information

| Item | Details |
|---|---|
| VMID | 100 |
| IP | 10.0.0.105 |
| RAM | 16 GB |
| CPU | 4 cores |
| Platform | Ubuntu (Hermes AI Agent) |
| **Backup Schedule** | **Daily 3:30 AM CST to PBS1** |
| **Backup Target** | **PBS1 local + PBS2 offsite replication** |

## Purpose

Hermes-Agent is the primary AI operations assistant responsible for:

- Scheduled Telegram infrastructure reports (8 AM / 8 PM CST)
- Email monitoring and bidirectional communication
- GitHub documentation maintenance
- Proxmox API and SSH-based metrics collection
- Backup verification and health monitoring

See [AI Assistant](ai-assistant.md) for full details.

---

# Ai.Assistant — VM 103 (Legacy)

## System Information

| Item | Details |
|---|---|
| VMID | 103 |
| RAM | 16 GB |
| CPU | 4 cores |
| Ballooning | Disabled |
| Service | homelab-bot.service (systemd) |

## Purpose

Original Telegram bot with local Ollama LLM integration.

## Technologies

- Python + Telegram Bot API
- Ollama — `qwen2.5:1.5b`
- Proxmox API

## Commands

| Command | Purpose |
|---|---|
| `/status` | Infrastructure reachability |
| `/vms` | Live VM status |
| `/checkbackups` | Recent backup summary |

---

# Windows Server 2025 (CorpDC)

## System Information

| Item | Details |
|---|---|
| Hostname | CorpDC |
| IP (LAN) | 10.0.0.45 |
| Tailscale IP | 100.120.40.4 |
| Platform | Windows Server 2025 (physical) |
| Domain | corp.local |
| Role | Domain Controller |

## Core Services

| Service | Purpose |
|---|---|
| Active Directory | Identity management |
| DNS | Internal name resolution (corp.local) |
| DHCP | LAN address assignment |
| Group Policy | Centralized Windows management |
| Windows Admin Center | Web-based server management (HTTPS :6600) |

## Backup Method

- Windows System State Backup → Samba share on UbuntuServer1 → PBS1 → PBS2

---

# PBS1

## System Information

| Item | Details |
|---|---|
| Hardware | Dell OptiPlex |
| IP (LAN) | 10.0.0.30 |
| IP (Backup NIC) | 192.168.50.2 |
| Role | Local Proxmox Backup Server |

## Datastores

| Datastore | Size | Used |
|---|---|---|
| pbs-4tb | 3.6 TB | ~624 GB |
| pbs-data | 1.8 TB | ~292 GB |

## Purpose

PBS1 provides local Proxmox VM backups, retention management, backup scheduling, and recovery capability.

---

# PBS2

## System Information

| Item | Details |
|---|---|
| Role | Offsite disaster recovery backup server |
| OS | Debian / Proxmox Backup Server |
| Tailscale IP | 100.98.162.11 |
| Connectivity | Tailscale |
| Location | Offsite (parents' house) |

## Purpose

PBS2 receives synchronized backups from PBS1 and provides offsite disaster recovery.

---

# Raspberry Pi IDS

## System Information

| Item | Details |
|---|---|
| IP | 10.0.0.170 |
| Tailscale IP | 100.71.77.1 |
| Role | IDS / traffic monitoring |

## Tools Used

- Bettercap (net.recon, net.probe, net.sniff)
- Switch port mirroring from TL-SG608E
- Logs synced to UbuntuServer1 → Grafana Alloy → Loki → Grafana

---

# Enterprise Wireless Infrastructure

## Access Point

| Item | Details |
|---|---|
| IP | 10.0.0.2 |
| Role | Enterprise AP — dual SSID |

The enterprise AP provides:
- Primary internal SSID (VLAN 1)
- Isolated guest wireless network (VLAN 10)

---

# OPNsense Firewall / VPN Router

| Item | Details |
|---|---|
| IP | 10.0.0.110 |
| Role | VPN router / firewall |

---

# Infrastructure Goals

The environment is designed to provide experience with:

- virtualization and VM management
- monitoring and observability
- disaster recovery and backup architecture
- centralized logging and SIEM tooling
- Active Directory and Windows enterprise services
- AI-assisted infrastructure operations
- security monitoring and network visibility
- operational automation and documentation
