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

|| System | IP | Purpose |
||---|---|---|
|| FortiGate 50E | 10.0.0.1 | Primary edge firewall, gateway, and wireless controller |
|| Dell PowerEdge T320 | 10.0.0.46 | Primary Proxmox VE virtualization host |
|| UbuntuServer1 (VM 102) | 10.0.0.42 | Main Linux infrastructure and services VM |
|| Hermes-Agent / Mara (VM 100) | 10.0.0.105 | Primary AI operations assistant |
|| Ai.Assistant (VM 103) | — | Legacy Telegram bot + Ollama LLM |
|| Windows Server 2025 (CorpDC) | 10.0.0.45 | Active Directory, DNS, DHCP, and Group Policy |
|| PBS1 | 10.0.0.30 | Local Proxmox Backup Server |
|| PBS2 | Tailscale | Offsite Proxmox Backup Server |
|| Raspberry Pi IDS | 10.0.0.170 | Traffic monitoring and IDS |
|| Pi-hole | 10.0.0.42 | DNS filtering and visibility |
|| Managed Switch (IronGateSwitch1) | 10.0.0.254 | VLANs, QoS, and port mirroring |
|| Beelink Mini S | 10.0.50.x | Dedicated honeypot server (Cowrie + OpenCanary) |

---

# High-Level Architecture

```text
Clients / Lab Devices
        |
TL-SG608E Managed Switch + FortiGate 50E Wireless Controller
        |
FortiGate 50E (10.0.0.1) — Primary Gateway, Firewall & Wireless Management
        |
        +---- INFRA-PROD-NET (10.0.0.0/24)
        |       |
        |       +---- Dell PowerEdge T320 (10.0.0.46)
        |       |       +---- VM 100: Hermes-Agent / Mara (10.0.0.105)
        |       |       +---- VM 102: UbuntuServer1 (10.0.0.42)
        |       |       +---- VM 103: Ai.Assistant
        |       |
        |       +---- CorpDC (10.0.0.45)
        |       +---- PBS1 (10.0.0.30)
        |       +---- Raspberry Pi IDS (10.0.0.170)
        |
        +---- IoT_Devices (10.0.20.0/24)
        |
        +---- Camera_Devices (10.0.30.0/24)
        |
        +---- Media_Devices (10.0.40.0/24)
        |
        +---- Servers / Honeypot (10.0.50.0/24)
        |       |
        |       +---- Beelink Mini S (Ubuntu Server)
        |               +---- Cowrie SSH Honeypot
        |               +---- OpenCanary Services
        |               +---- Wazuh Agent
        |
        +---- Guest_Devices (10.0.60.0/24)

Backup Network (192.168.50.0/24)
        |
        +---- Dell PowerEdge (192.168.50.1) ←→ PBS1 (192.168.50.2)

Wazuh Security Monitoring
        |
        +---- Wazuh Manager (UbuntuServer1)
        +---- Wazuh Agents (multiple systems)
        |       +---- UbuntuServer1
        |       +---- CorpDC
        |       +---- Beelink Mini S (Cowrie + OpenCanary)
        |
        +---- Wazuh Dashboard (Grafana integration)
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
