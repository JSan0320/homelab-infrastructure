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
- remote management via Tailscale

---

# Core Infrastructure

| System | IP | Purpose |
|---|---|---|
| FortiGate 50E | 10.0.0.1 | Primary edge firewall and gateway |
| Dell PowerEdge T320 | 10.0.0.46 | Primary Proxmox VE virtualization host |
| UbuntuServer1 (VM 102) | 10.0.0.42 | Main Linux infrastructure and services VM |
| Hermes-Agent / Mara (VM 100) | 10.0.0.105 | Primary AI operations assistant |
| Ai.Assistant (VM 103) | — | Legacy Telegram bot + Ollama LLM |
| Windows Server 2025 (CorpDC) | 10.0.0.45 | Active Directory, DNS, DHCP, and Group Policy |
| PBS1 | 10.0.0.30 | Local Proxmox Backup Server |
| PBS2 | Tailscale | Offsite Proxmox Backup Server |
| Raspberry Pi IDS | 10.0.0.170 | Traffic monitoring and IDS |
| Pi-hole | 10.0.0.42 | DNS filtering and visibility |
| Managed Switch (IronGateSwitch1) | 10.0.0.254 | VLANs, QoS, and port mirroring |
| Enterprise AP | 10.0.0.2 | Main and guest wireless networks |

---

# High-Level Architecture

```text
Clients / Lab Devices
        |
TL-SG608E Managed Switch + Enterprise AP
        |
Core LAN / VLANs (10.0.0.0/24)
        |
+---------------------------+
|  Proxmox VE Host          |
|  Dell PowerEdge T320      |
|  10.0.0.46                |
+---------------------------+
        |
        +---- VM 100: Hermes-Agent / Mara (10.0.0.105)
        |       - Scheduled Telegram reports (8 AM / 8 PM CDT)
        |       - Email monitoring and replies
        |       - GitHub documentation maintenance
        |       - Proxmox API + SSH metrics collection
        |
        +---- VM 102: UbuntuServer1 (10.0.0.42)
        |       - Samba (network shares)
        |       - Grafana / Prometheus / Loki
        |       - Grafana Alloy (log/metric pipeline)
        |       - Wazuh (SIEM)
        |       - goflow2 (NetFlow)
        |       - Jellyfin / Plex
        |       - Webmin / Homarr / Pi-hole
        |
        +---- VM 103: Ai.Assistant (legacy)
                - Ollama (qwen2.5:1.5b)
                - Python Telegram Bot
                - /status, /vms, /checkbackups

+---------------------------+
|  Windows Server 2025      |
|  CorpDC (physical)        |
|  10.0.0.45                |
+---------------------------+
        |
        - Active Directory (corp.local)
        - DNS
        - DHCP
        - Group Policy
        - Windows Admin Center

+---------------------------+
|  PBS1                     |
|  Local Backup Server      |
|  10.0.0.30                |
+---------------------------+
        |
        +---- Dedicated backup NIC (192.168.50.0/24)
        +---- Offsite Sync (Tailscale) ---- PBS2 (100.98.162.11)

+---------------------------+
|  Raspberry Pi IDS         |
|  10.0.0.170               |
+---------------------------+
        |
        - Bettercap (net.recon, net.probe, net.sniff)
        - Port mirror from switch port 3
        - Logs → UbuntuServer1 → Alloy → Loki → Grafana
```

---

# Monitoring Pipeline

```text
Infrastructure Devices
        |
Exporters / Agents / Syslog / NetFlow / IDS Logs
        |
        +---- Prometheus (metrics)
        +---- Loki (logs via Grafana Alloy)
        +---- Wazuh (security events, 9 agents)
        +---- goflow2 (NetFlow from softflowd)
        +---- Netdata (node metrics, claimed to cloud)
        |
Grafana Dashboards
        |
Telegram Reports (Hermes-Agent, 8 AM / 8 PM CDT)
```

---

# Backup Architecture

```text
Proxmox VMs
        |
PBS1 (local — 10.0.0.30)
        |
PBS2 (offsite — Tailscale 100.98.162.11)

Samba data (/srv/samba/FILES)
        |
/mnt/backup (rsync daily 02:00)

CorpDC (physical Windows Server)
        |
Windows System State Backup
        |
Samba share on UbuntuServer1
        |
PBS1 → PBS2
```

---

# Remote Access

| Method | Purpose |
|---|---|
| Tailscale mesh | Primary remote management overlay |
| SSH (via Tailscale) | Linux host administration |
| Windows Admin Center (HTTPS :6600) | CorpDC remote management |
| RDP | Windows desktop access |
| Proxmox web UI | VM management |
| PBS web UI | Backup management |
