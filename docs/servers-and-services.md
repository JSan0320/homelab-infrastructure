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

| System | Purpose |
|---|---|
| Dell PowerEdge T320 | Primary Proxmox virtualization host |
| UbuntuServer1 | Main Linux infrastructure services VM |
| Windows Server 2025 (CorpDC) | Active Directory, DNS, DHCP, and Group Policy |
| PBS1 | Local Proxmox Backup Server |
| PBS2 | Offsite Proxmox Backup Server |
| AI Assistant VM | Telegram AI monitoring and automation |
| Raspberry Pi IDS | Traffic monitoring and IDS |
| Pi-hole | DNS filtering and visibility |

---

# Proxmox VE Host

## Hardware

| Item | Details |
|---|---|
| System | Dell PowerEdge T320 |
| Role | Primary virtualization host |
| IP | 10.0.0.46 |
| Hypervisor | Proxmox VE |

## Purpose

The Proxmox host provides:
- virtualization
- VM management
- backup scheduling
- centralized compute infrastructure

## Hosted VMs

| VM | Purpose |
|---|---|
| UbuntuServer1 | Linux services |
| AI Assistant VM | Telegram/Ollama automation |
| Windows test VMs | Administrative/testing systems |

---

# UbuntuServer1

## System Information

| Item | Details |
|---|---|
| IP | 10.0.0.42 |
| Role | Primary Linux services VM |
| Platform | Ubuntu Server |

## Main Services

| Service | Purpose |
|---|---|
| Samba | Network file shares |
| Grafana | Monitoring dashboards |
| Prometheus | Metrics collection |
| Loki | Centralized logging |
| Wazuh | SIEM/security monitoring |
| Jellyfin | Media services |
| goflow2 | NetFlow collection |
| Webmin | Web-based administration |

## Important Ports

| Port | Service |
|---|---|
| 3000 | Grafana |
| 9090 | Prometheus |
| 8096 | Jellyfin |
| 10000 | Webmin |
| 8080 | goflow2 |

## Samba Shares

Primary share path:

```text
/srv/samba/FILES
```

Purpose:
- infrastructure storage
- backup staging
- Windows backup storage
- shared files

---

# Windows Server 2025 (CorpDC)

## System Information

| Item | Details |
|---|---|
| Hostname | CorpDC |
| IP | 10.0.0.45 |
| Platform | Windows Server 2025 |
| Role | Domain Controller |

## Core Services

| Service | Purpose |
|---|---|
| Active Directory | Identity management |
| DNS | Internal name resolution |
| DHCP | Address assignment |
| Group Policy | Centralized Windows management |

## Domain Information

| Item | Value |
|---|---|
| Domain | corp.local |

## Purpose

The Windows Server environment is used to practice:
- enterprise Windows administration
- identity services
- centralized DNS/DHCP
- Group Policy management
- Active Directory administration

## Backup Method

The server uses:
- Windows System State Backup

Stored to:
- Samba share on UbuntuServer1

---

# PBS1

## System Information

| Item | Details |
|---|---|
| Hardware | Dell OptiPlex |
| IP | 10.0.0.30 |
| Role | Local backup server |

## Purpose

PBS1 provides:
- local Proxmox VM backups
- retention management
- backup scheduling
- recovery capability

## Backup Network

Dedicated backup interface:

| Device | IP |
|---|---|
| Proxmox Backup NIC | 192.168.50.1 |
| PBS1 Backup NIC | 192.168.50.2 |

---

# PBS2

## System Information

| Item | Details |
|---|---|
| Role | Offsite backup server |
| Connectivity | Tailscale |
| Purpose | Disaster recovery storage |

## Purpose

PBS2 provides:
- offsite backup replication
- disaster recovery capability
- backup redundancy
- protection against site-level failure

---

# AI Assistant VM

## System Information

| Item | Details |
|---|---|
| VM Name | Ai.Assistant |
| VMID | 103 |
| RAM | 16GB |
| CPU | 4 cores |
| Ballooning | Disabled |

## Purpose

The AI Assistant VM provides:
- Telegram monitoring bot
- Proxmox API integrations
- backup reporting
- VM visibility
- automated infrastructure reporting

## Technologies Used

- Python
- Ollama
- Telegram Bot API
- Proxmox API
- systemd

## Current Commands

| Command | Purpose |
|---|---|
| `/status` | Infrastructure reachability |
| `/vms` | VM status visibility |
| `/checkbackups` | Backup reporting |

---

# Raspberry Pi IDS

## System Information

| Item | Details |
|---|---|
| IP | 10.0.0.170 |
| Role | IDS / traffic monitoring |

## Purpose

The Raspberry Pi IDS provides:
- network visibility
- traffic monitoring
- DNS inspection
- passive traffic analysis

## Tools Used

- Bettercap
- switch port mirroring

---

# Pi-hole

## Purpose

Pi-hole provides:
- DNS filtering
- ad blocking
- DNS visibility
- centralized DNS monitoring

## Benefits

- improved visibility
- traffic reduction
- DNS analytics
- centralized filtering

---

# Enterprise Wireless Infrastructure

## Enterprise AP

The enterprise access point provides:
- primary wireless network
- guest wireless network
- VLAN-backed guest isolation

## Purpose

The AP is used to practice:
- enterprise wireless management
- guest segmentation
- VLAN integration

---

# OpenWrt Infrastructure

## Purpose

The OpenWrt environment provides:
- remote routing
- Tailscale integration
- offsite connectivity
- disaster recovery networking

## Future Goals

Future plans include:
- warm-site infrastructure
- remote Proxmox recovery host
- offsite VM restoration

---

# Infrastructure Goals

The environment is designed to provide experience with:

- virtualization
- monitoring
- disaster recovery
- centralized logging
- SIEM tooling
- Active Directory
- backup architecture
- infrastructure automation
- security monitoring
- operational visibility
