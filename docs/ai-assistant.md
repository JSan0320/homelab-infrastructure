# AI Operations Assistant

## Overview

The homelab runs two AI assistant systems with distinct roles:

| System | VM | Role | Status |
|---|---|---|---|
| **Hermes-Agent (Mara)** | VM 100 | Primary AI ops assistant — scheduling, reporting, email, documentation | Active |
| **Ai.Assistant (Legacy)** | VM 103 | Original Telegram bot + Ollama local LLM | Active (legacy) |

---

# Hermes-Agent (Mara) — Primary System

## Purpose

Hermes-Agent is the primary AI operations assistant responsible for:

- Scheduled infrastructure reporting via Telegram
- Email monitoring and bidirectional communication
- GitHub documentation maintenance
- Infrastructure data collection and analysis
- Proxmox API and SSH-based metrics gathering

## VM Configuration

| Item | Value |
|---|---|
| VM Name | Hermes-Agent |
| VMID | 100 |
| IP | 10.0.0.105 |
| RAM | 8 GB |
| CPU | 2 cores |
| Platform | Ubuntu (Hermes AI Agent) |

## Automated Jobs

### Daily Infrastructure Reports

Two scheduled Telegram reports delivered daily to the home channel:

| Report | Schedule | Cron (UTC) |
|---|---|---|
| Morning Report | 8:00 AM CDT | `0 13 * * *` |
| Evening Report | 8:00 PM CDT | `0 1 * * *` |

**Report contents:**

- Proxmox node status (Dell) — CPU, RAM, disk, uptime via SSH
- VM summary — status, CPU, RAM, uptime for all VMs
- PBS backup status and datastore usage
- Reachability checks (Dell Proxmox, UbuntuServer1, PBS1, CorpDC)
- Active warnings

**Script:** `~/.hermes/scripts/homelab_daily_report.py`

### Email Monitor

Polls `mara.ai.assistant@gmail.com` every 30 minutes for messages from `jsanford0320@outlook.com`.

- Replies via email automatically
- Detects Telegram relay requests and sends Telegram messages
- Tracks processed email UIDs to prevent duplicate processing

**Script:** `~/.hermes/scripts/email_monitor.py`

## Data Sources

| Source | Method | Data Used |
|---|---|---|
| Proxmox API | HTTP (token auth) | VM inventory, VM CPU/RAM/uptime |
| Proxmox host (Dell) | SSH (`ssh proxmox`) | Node CPU, RAM, disk, uptime |
| PBS API | HTTP (token auth) | Backup tasks, datastore usage |
| Ping checks | ICMP | Host reachability |
| Gmail | IMAP/SMTP | Email monitoring and replies |
| Telegram Bot API | HTTP | Telegram message delivery |

## Node Metrics Collection (Hybrid Approach)

Node-level metrics are collected via SSH using machine-readable commands:

| Metric | Command |
|---|---|
| CPU | `awk '/^cpu /' /proc/stat` (two reads, 1s delta) |
| RAM | `awk '/MemTotal\|MemAvailable/' /proc/meminfo` |
| Disk | `df -B1 --output=size,used /` |
| Uptime | `cut -d' ' -f1 /proc/uptime` |

Proxmox API is used for VM inventory and VM resource stats.

## Documentation Responsibility

Hermes-Agent (Mara) is responsible for maintaining the GitHub repository:

- Repository: `JSan0320/homelab-infrastructure`
- Commits infrastructure changes, changelog entries, and documentation updates
- Small updates do not require approval
- Never commits credentials, secrets, or sensitive configuration

## Security Principles

- Read-only infrastructure monitoring by default
- Credentials stored in local `.env` files — never committed to GitHub
- No destructive actions without explicit approval
- No VM restarts, config changes, or service modifications without approval

---

# Ai.Assistant (Legacy) — VM 103

## Purpose

The legacy Ai.Assistant VM provides the original Telegram bot with local LLM capabilities.

## VM Configuration

| Item | Value |
|---|---|
| VM Name | Ai.Assistant |
| VMID | 103 |
| RAM | 16 GB |
| CPU | 4 cores |
| Ballooning | Disabled |
| Service | homelab-bot.service (systemd) |

## Technologies

- Python Telegram Bot
- Ollama (local LLM)
- Model: `qwen2.5:1.5b`
- Proxmox API integration

## Commands

| Command | Purpose |
|---|---|
| `/status` | Infrastructure reachability check |
| `/vms` | Live Proxmox VM status |
| `/checkbackups` | Recent backup task summary |

## Service Management

```bash
sudo systemctl status homelab-bot
sudo systemctl restart homelab-bot
journalctl -u homelab-bot -f
```

---

# Architecture

```text
Telegram (Jake)
      |
      +---> Hermes-Agent (VM 100) -------> Proxmox API
      |           |                 -----> SSH (proxmox)
      |           |                 -----> PBS API
      |           |                 -----> Gmail IMAP/SMTP
      |           |                 -----> GitHub (docs)
      |           |
      |           +---> Scheduled Reports (8 AM / 8 PM CDT)
      |           +---> Email Monitor (every 30 min)
      |
      +---> Ai.Assistant (VM 103) -------> Proxmox API
                  |                -----> Ollama (qwen2.5:1.5b)
                  |
                  +---> /status, /vms, /checkbackups
```

---

# Future Improvements

- Wazuh alert summaries in daily reports
- Grafana anomaly detection integration
- PBS2 offsite sync status
- Automated changelog generation from infrastructure changes
- Alert escalation for critical failures
