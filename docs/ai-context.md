# AI Homelab Context

## Environment Overview

This homelab is a hybrid Windows/Linux infrastructure environment focused on:

- virtualization
- monitoring and observability
- backup and disaster recovery
- security monitoring
- AI-assisted operations
- centralized observability

Primary hypervisor:
- Proxmox VE 9.1 on Dell PowerEdge T320

Core LAN:
- 10.0.0.0/24

---

# Core Systems

## Proxmox VE Host

IP:
- 10.0.0.46
- Backup NIC: 192.168.50.1

Purpose:
- virtualization platform
- VM hosting
- backup orchestration

---

## Hermes-Agent / Mara (VM 100) — Primary AI System

VMID:
- 100

IP:
- 10.0.0.105

Purpose:
- Primary AI operations assistant
- Scheduled Telegram infrastructure reports (8 AM / 8 PM CDT)
- Email monitoring and bidirectional communication
- GitHub documentation maintenance
- Proxmox API + SSH metrics collection

Scripts:
- `~/.hermes/scripts/homelab_daily_report.py`
- `~/.hermes/scripts/email_monitor.py`

Credentials (local, never committed to GitHub):
- `~/.pve.env` — Proxmox API token
- `~/.pbs.env` — PBS API token
- `~/.wazuh.env` — Wazuh credentials
- `~/.grafana.env` — Grafana service account token
- `~/.gmail.env` — Gmail app password
- `~/.hermes/.env` — Telegram bot token + chat ID
- `~/.github.env` — GitHub token (read/write, homelab-infrastructure repo)

---

## UbuntuServer1 (VM 102)

VMID:
- 102

IP:
- 10.0.0.42

Primary Linux services server.

Runs:
- Samba (network shares — /srv/samba/FILES)
- Grafana (http://10.0.0.42:3000)
- Prometheus (http://10.0.0.42:9090)
- Loki
- Grafana Alloy (log/metric pipeline)
- Wazuh (SIEM — API: https://10.0.0.42:55000, Dashboard: https://10.0.0.42:443)
- goflow2 (NetFlow — port 2055)
- Jellyfin
- Plex
- Webmin
- Homarr (http://10.0.0.42:7575)
- Pi-hole (DNS filtering)

Storage:
- /srv/samba/FILES — WD 2TB SATA (production share)
- /mnt/backup — WD 2TB SATA (rsync mirror, daily 02:00)

---

## CorpDC

Physical Windows Server 2025 system.

IP:
- 10.0.0.45
- Tailscale: 100.120.40.4

Domain:
- corp.local

Runs:
- Active Directory
- DNS
- DHCP
- Group Policy
- Windows Admin Center (HTTPS :6600)

---

## Ai.Assistant (VM 103) — Legacy

VMID:
- 103

Purpose:
- Legacy Telegram AI assistant
- Ollama local LLM

Runs:
- Python Telegram bot (homelab-bot.service)
- Ollama (qwen2.5:1.5b)

Resources:
- 16 GB RAM
- 4 CPU cores
- ballooning disabled

Commands: `/status`, `/vms`, `/checkbackups`

---

## PBS1

IP:
- 10.0.0.30 (LAN)
- 192.168.50.2 (backup NIC)

Purpose:
- Local Proxmox Backup Server
- Datastores: `pbs-4tb` (3.6 TB), `pbs-data` (1.8 TB)

---

## PBS2

Purpose:
- Offsite disaster recovery backup server

Connectivity:
- Tailscale: 100.98.162.11

---

## Raspberry Pi IDS

IP:
- 10.0.0.170
- Tailscale: 100.71.77.1

Purpose:
- Bettercap IDS (net.recon, net.probe, net.sniff)
- Switch port mirror from TL-SG608E port 3
- Logs → rsync → UbuntuServer1 → Grafana Alloy → Loki → Grafana

---

# Network

| Device | IP |
|---|---|
| Xfinity Gateway | 10.0.0.1 |
| Enterprise AP | 10.0.0.2 |
| Proxmox Host | 10.0.0.46 |
| UbuntuServer1 | 10.0.0.42 |
| CorpDC | 10.0.0.45 |
| Hermes-Agent | 10.0.0.105 |
| PBS1 | 10.0.0.30 |
| OPNsense | 10.0.0.110 |
| Pi IDS | 10.0.0.170 |
| Switch | 10.0.0.254 |

VLANs:
- VLAN 1: Internal (ports 1–7)
- VLAN 10: Guest (port 8)

Backup subnet: 192.168.50.0/24

---

# Monitoring Stack

The environment uses:
- Grafana (dashboards)
- Prometheus (metrics)
- Loki (logs)
- Grafana Alloy (pipeline)
- Wazuh (SIEM, 9 agents)
- goflow2 (NetFlow)
- Netdata (node metrics, cloud-claimed)
- Bettercap IDS
- Hermes-Agent Telegram reports

---

# Backup Architecture

```text
Proxmox VMs → PBS1 (local) → PBS2 (offsite via Tailscale)

Samba data (/srv/samba/FILES) → /mnt/backup (rsync daily 02:00)

CorpDC → Windows System State Backup → Samba → PBS1 → PBS2
```

---

# GitHub Documentation

Repository: `JSan0320/homelab-infrastructure`

Maintained by Hermes-Agent (Mara). Updated automatically on infrastructure changes.
No credentials or secrets are ever committed.

---

# AI Assistant Rules

The assistant must:
- remain read-only for infrastructure changes unless explicitly approved
- never claim actions were performed unless verified by tool output
- never invent infrastructure details
- use live API and SSH data for reports
- prefer concise technical responses
- state uncertainty when unsure
- never commit credentials, secrets, tokens, or sensitive configuration to GitHub
