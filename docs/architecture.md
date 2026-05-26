# Architecture Overview

## Summary

This homelab is designed as a hybrid enterprise-style environment focused on:

- virtualization
- monitoring and observability
- backup and disaster recovery
- Windows and Linux administration
- security tooling
- automation
- AI-assisted operations

The environment combines:
- Proxmox virtualization
- Linux infrastructure services
- Windows enterprise services
- centralized monitoring
- offsite disaster recovery
- remote management

---

# Core Infrastructure

| System | Purpose |
|---|---|
| Dell PowerEdge T320 | Primary Proxmox VE virtualization host |
| UbuntuServer1 | Main Linux infrastructure and services VM |
| Windows Server 2025 (CorpDC) | Active Directory, DNS, DHCP, and Group Policy |
| PBS1 | Local Proxmox Backup Server |
| PBS2 | Offsite Proxmox Backup Server |
| AI Assistant VM | Telegram AI monitoring and automation |
| Raspberry Pi IDS | Traffic monitoring and IDS |
| Pi-hole | DNS filtering and visibility |
| Managed Switch | VLANs, QoS, and port mirroring |
| Enterprise AP | Main and guest wireless networks |
| OpenWrt Router | Remote/offsite routing and Tailscale support |

---

# High-Level Architecture

```text
Clients / Lab Devices
        |
Managed Switch + Enterprise AP
        |
Core LAN / VLANs
        |
+----------------------+
|  Proxmox VE Host     |
|  Dell PowerEdge T320 |
+----------------------+
        |
        +---- UbuntuServer1
        |       - Samba
        |       - Grafana
        |       - Prometheus
        |       - Loki
        |       - Wazuh
        |       - Jellyfin
        |       - goflow2
        |
        +---- AI Assistant VM
                - Ollama
                - Telegram Bot
                - Proxmox API Automation
                - Daily Health Reports

+----------------------+
| Windows Server 2025  |
| CorpDC               |
+----------------------+
        |
        - Active Directory
        - DNS
        - DHCP
        - Group Policy

+----------------------+
| PBS1                 |
| Local Backup Server  |
+----------------------+
        |
        +---- Offsite Sync ---- PBS2

+----------------------+
| Raspberry Pi IDS     |
+----------------------+
        |
        - Bettercap
        - Traffic Monitoring
        - Port Mirroring

+----------------------+
| Pi-hole              |
+----------------------+
        |
        - DNS Filtering
        - DNS Visibility
