# Change Log

This document tracks major infrastructure changes, deployments, upgrades, and architectural improvements within the homelab.

---

# 2026

## May 2026

### Added Windows Server 2025 Domain Controller

Implemented:
- Active Directory
- DNS
- DHCP
- Group Policy

Created:
- `corp.local` domain

Purpose:
- enterprise Windows administration practice
- identity services
- centralized DNS and DHCP management

---

### Built AI Operations Assistant VM

Created dedicated Ubuntu VM for:
- Telegram operations bot
- Ollama local LLM
- Proxmox API integrations
- backup visibility
- daily health reporting

Implemented:
- `/status`
- `/vms`
- `/checkbackups`

Configured:
- systemd service persistence
- daily automated reporting

---

### Expanded AI VM Resources

Increased AI VM resources to:
- 16GB RAM
- 4 CPU cores
- memory ballooning disabled

Purpose:
- improve Ollama performance
- reduce response latency
- improve model stability

---

### Implemented Proxmox API Integration

Connected Telegram AI assistant to:
- Proxmox node API
- VM status endpoints

Purpose:
- live VM visibility
- infrastructure monitoring
- automated reporting

---

### Added Offsite PBS Replication

Configured:
- PBS1 → PBS2 offsite synchronization

Purpose:
- disaster recovery
- offsite redundancy
- protection against site failure

Connectivity:
- Tailscale
- remote OpenWrt infrastructure

---

### Implemented Windows System State Backup Workflow

Configured:
- Windows Server System State Backup
- Samba backup storage
- PBS protection through UbuntuServer1

Purpose:
- Active Directory recovery
- DNS recovery
- DHCP recovery
- infrastructure restoration

---

### Improved Monitoring Stack

Expanded monitoring capabilities:
- Grafana
- Prometheus
- Loki
- Wazuh
- goflow2

Purpose:
- observability
- centralized monitoring
- infrastructure visibility
- security monitoring

---

### Cleaned Up UbuntuServer1 Services

Removed:
- unused Kubernetes components
- legacy services
- stale workloads

Result:
- dramatically reduced system load
- improved responsiveness
- improved resource availability

---

### Added Dedicated Backup Network

Implemented isolated backup network between:
- Proxmox VE host
- PBS1

Subnet:
- `192.168.50.0/24`

Purpose:
- improved backup performance
- reduced LAN congestion
- isolated backup traffic

---

### Expanded Documentation Initiative

Created:
- public GitHub homelab repository
- architecture documentation
- network documentation
- backup/disaster recovery documentation
- AI assistant documentation

Purpose:
- infrastructure tracking
- portfolio development
- operational visibility
- interview preparation

---

# Future Planned Changes

## Planned Warm Site

Future goals:
- offsite Proxmox host
- restore-ready infrastructure
- rapid disaster recovery capability

---

## Planned Automation Expansion

Future goals:
- Ansible automation
- Infrastructure-as-Code
- automated deployment workflows

---

## Planned Security Improvements

Future goals:
- enhanced detection engineering
- automated alerting
- improved segmentation
- centralized log analysis
- expanded IDS visibility
