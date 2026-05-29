# AI Homelab Context

## Environment Overview

This homelab is a hybrid Windows/Linux infrastructure environment focused on:

- virtualization
- monitoring
- backup and disaster recovery
- security monitoring
- AI-assisted operations
- centralized observability

Primary hypervisor:
- Proxmox VE on Dell PowerEdge T320

Core LAN:
- 10.0.0.0/24

---

# Core Systems

## Proxmox VE Host

IP:
- 10.0.0.46

Purpose:
- virtualization platform
- VM hosting
- backup orchestration

---

## UbuntuServer1

VMID:
- 102

IP:
- 10.0.0.42

Primary Linux services server.

Runs:
- Samba
- Grafana
- Prometheus
- Loki
- Wazuh
- Jellyfin
- goflow2
- Webmin

Primary Samba path:
- /srv/samba/FILES

---

## CorpDC

Physical Windows Server 2025 system.

IP:
- 10.0.0.45

Domain:
- corp.local

Runs:
- Active Directory
- DNS
- DHCP
- Group Policy

---

## AI Assistant VM

VMID:
- 103

Purpose:
- Telegram AI assistant
- Ollama
- Proxmox API integration
- infrastructure monitoring

Runs:
- Python Telegram bot
- Ollama
- qwen2.5:1.5b

Resources:
- 16GB RAM
- 4 CPU cores
- ballooning disabled

---

## PBS1

IP:
- 10.0.0.30

Purpose:
- local Proxmox Backup Server

Connected through dedicated backup network:
- 192.168.50.0/24

---

## PBS2

Purpose:
- offsite disaster recovery backup server

Connectivity:
- Tailscale
- remote OpenWrt network

---

## Raspberry Pi IDS

IP:
- 10.0.0.170

Purpose:
- Bettercap IDS
- traffic monitoring
- mirrored traffic visibility

---

# Monitoring Stack

The environment uses:
- Grafana
- Prometheus
- Loki
- Wazuh
- goflow2
- Telegram AI reporting

---

# Backup Architecture

Backup flow:

CorpDC
↓
Windows System State Backup
↓
Samba Share on UbuntuServer1
↓
PBS1 Backup
↓
PBS2 Offsite Sync

---

# AI Assistant Rules

The assistant must:
- remain read-only
- never claim actions were performed unless verified
- never invent infrastructure
- answer using provided context and live data
- prefer concise technical responses
- state uncertainty when unsure
