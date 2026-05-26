# Homelab Infrastructure

This repository documents my hybrid homelab focused on virtualization, monitoring, disaster recovery, networking, security, and automation.

## Core Goals

- Build hands-on infrastructure experience using enterprise-style tools
- Practice Windows and Linux system administration
- Implement monitoring, logging, and backup workflows
- Learn security operations concepts through Wazuh, IDS, logging, and traffic analysis
- Document the environment clearly for troubleshooting, learning, and portfolio use

## Main Technologies

- Proxmox VE
- Proxmox Backup Server
- Ubuntu Server
- Windows Server 2025
- Active Directory, DNS, DHCP, and GPO
- Samba
- Wazuh
- Prometheus, Grafana, Loki
- Tailscale
- Raspberry Pi IDS / Bettercap
- Pi-hole
- Managed switch VLANs and port mirroring
- Python Telegram AI operations bot
- Ollama local LLM

## Highlights

- Proxmox virtualization host running multiple lab VMs
- Dedicated Proxmox Backup Server with local and offsite backup strategy
- Windows Server domain controller for AD, DNS, DHCP, and GPO practice
- Ubuntu Server providing Samba, monitoring, Wazuh, Jellyfin, and logging services
- AI-powered Telegram assistant that reports VM status, Proxmox health, and backup status
- Tailscale-based remote access and offsite backup connectivity
- Network monitoring with port mirroring, traffic capture, and IDS tooling

## Documentation

- [Architecture Overview](docs/architecture.md)
- [Network Map](docs/network-map.md)
- [Servers and Services](docs/servers-and-services.md)
- [Monitoring](docs/monitoring.md)
- [Backups and Disaster Recovery](docs/backups-and-dr.md)
- [Security](docs/security.md)
- [AI Assistant](docs/ai-assistant.md)
- [Change Log](docs/change-log.md)
