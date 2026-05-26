# AI Operations Assistant

## Overview

The AI Operations Assistant is a Telegram-based homelab monitoring bot running on a dedicated Ubuntu VM.

It provides mobile access to infrastructure status, Proxmox VM information, backup visibility, and automated daily reports.

## Purpose

The goal of this assistant is to make the homelab easier to monitor remotely without constantly logging into Proxmox, PBS, Grafana, or individual servers.

## Technologies Used

- Ubuntu Server VM
- Python
- Python virtual environment
- Telegram Bot API
- Ollama
- Local LLM: qwen2.5:1.5b
- Proxmox API
- systemd service
- Proxmox Backup Server task data

## VM Configuration

| Item | Value |
|---|---|
| VM Name | Ai.Assistant |
| VMID | 103 |
| RAM | 16 GB |
| CPU | 4 cores |
| Ballooning | Disabled |
| Runtime | Python + Ollama |
| Service Name | homelab-bot.service |

## Current Commands

| Command | Purpose |
|---|---|
| `/status` | Checks key infrastructure reachability |
| `/vms` | Shows live Proxmox VM status |
| `/checkbackups` | Shows recent backup task summary |

## Daily Report

The bot sends an automatic daily report at 8:00 AM Central Time.

The report includes:

- Proxmox node health
- CPU usage
- RAM usage
- disk usage
- uptime
- reachability checks
- VM status and resource usage
- backup summary

## Current Data Sources

| Source | Data Used |
|---|---|
| Ping checks | Basic online/offline reachability |
| Proxmox API | Node stats and VM status |
| Proxmox task history | Backup task visibility |
| Telegram API | Bot messaging |
| Ollama | Local AI response generation |

## Architecture

```text
Telegram
   ↓
Python Bot
   ↓
Proxmox API / Ping Checks / Backup Tasks
   ↓
Local Ollama LLM
   ↓
Telegram Response

## Service Management

The bot runs as a persistent systemd service.

```bash
sudo systemctl status homelab-bot
sudo systemctl restart homelab-bot
journalctl -u homelab-bot -f
```

## Design Principle

The assistant is designed so Python gathers facts first, then the AI summarizes or explains them.

```text
Python gathers real infrastructure data
        ↓
APIs and scripts verify truth
        ↓
AI summarizes results
        ↓
Telegram delivers the report
```

This avoids hallucinations and keeps monitoring output grounded in real system data.

## Security Notes

- The Proxmox API token uses read-only permissions where possible.
- Secrets are stored in a local `.env` file and are not committed to GitHub.
- The bot currently focuses on read-only monitoring and reporting.
- Write actions such as restarting VMs or changing services are intentionally avoided for safety.

## Future Improvements

Planned improvements include:

- PBS datastore usage reporting
- PBS2 offsite sync status reporting
- Wazuh alert summaries
- Grafana/Prometheus metric summaries
- Telegram alerts for offline systems
- disaster recovery status checks
- optional controlled remediation actions
