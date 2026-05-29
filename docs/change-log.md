# Change Log

This document tracks major infrastructure changes, deployments, upgrades, and architectural improvements within the homelab.

---

# 2026

## May 27 2026

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


-----------------------------------------
#2026

#May 29 2026


### Storage Investigation and Cleanup

Investigated:

* missing fourth drive in Dell PowerEdge T320
* inactive `vmstore2` storage definition
* Samba storage architecture

Discovered:

* Seagate ST1200MM0088 1.2TB 2.5" SAS drive installed in T320
* drive contained foreign RAID metadata from another server
* drive was not being used by Proxmox or Linux

Completed:

* cleared foreign RAID configuration
* removed stale `vmstore2` storage entry
* verified drive was safe to remove

Purpose:

* identify unused hardware
* clean up Proxmox storage configuration
* document physical storage inventory

---

### Verified Samba Storage Architecture

Confirmed:

Main Samba Storage

* WD20EARZ 2TB SATA
* mounted at `/srv/samba/FILES`
* approximately 167GB used
* approximately 1.6TB free

Backup Storage

* WD20EARZ 2TB SATA
* mounted at `/mnt/backup`
* contains matching copy of Samba data

Verified Largest Data Sets:

* Movies (~99GB)
* Samsung Pics & Vids (~27GB)
* DayZ Backup (~20GB)
* CorpDC Backups (~17GB)

Purpose:

* validate local redundancy
* verify backup architecture
* establish storage baseline

---

### Updated Storage Expansion Plan

Determined:

* Proxmox VM storage is the current storage bottleneck
* Samba storage is not capacity constrained
* existing 2TB backup drive should remain dedicated to local redundancy

Future Plan:

* retain dual 2TB Samba architecture
* reserve 1.2TB SAS drives for secondary PowerEdge server
* add additional 3.5-inch SATA drive for VM storage expansion

Purpose:

* maintain data redundancy
* increase available VM storage
* avoid disrupting existing backup workflows

---

### Updated VM Configuration

Modified:

* AI.Assistant VM configured for automatic startup on Proxmox host boot

Purpose:

* ensure service availability after host reboot
* align startup behavior with UbuntuServer1

