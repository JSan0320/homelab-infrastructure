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


# May 29 2026


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

-----------------------------------------------------------------------

# Homelab Change Log – Homarr Deployment and Homepage Retirement Plan

**Date:** 2026-05-30

## Summary

Implemented Homarr as the new primary homelab dashboard platform on UbuntuServer1. Homarr is intended to replace the existing Homepage deployment due to easier management, a modern interface, drag-and-drop configuration, and better integration capabilities.

## Changes Implemented

### Homarr Installation

**Host:** UbuntuServer1
**IP Address:** 10.0.0.42

Homarr was deployed as a Docker container using Docker Compose.

**Directory:**

```text
~/homarr
```

**Access URL:**

```text
http://10.0.0.42:7575
```

### Configuration Notes

* Generated and configured a valid `SECRET_ENCRYPTION_KEY`.
* Resolved startup failure caused by missing environment variable.
* Verified container startup and successful web interface access.
* Created initial Homarr board:

```text
Jakes_Homelab
```

## Dashboard Organization

Current planned dashboard structure:

### Infrastructure

* Proxmox
* UbuntuServer1
* CorpDC
* Syslog Server

### Monitoring

* Grafana
* Wazuh Dashboard
* Netdata

### Network

* Xfinity Gateway
* TP-Link Switch
* Enterprise AP
* Tailscale

### Backups

* PBS1
* PBS2

### Media

* Jellyfin
* Plex

### Security

* Bitwarden Vault
* Wazuh

### Management

* Proxmox
* Webmin
* Windows Admin Center

### AI

* AI.Assistant
* Ollama
* Telegram Bot

## Windows Admin Center Deployment

Windows Admin Center was installed on CorpDC.

**Server:**

```text
CorpDC
10.0.0.45
```

### Access URL

```text
https://10.0.0.45:6600
```

### Notes

* Initial installation completed successfully.
* Windows Admin Center services verified running.
* Firewall rule manually created for TCP port 6600.
* Browser-based management of Windows Server 2025 confirmed operational.
* Future goal is to evaluate moving Windows Admin Center to TCP 443.

## Tailscale Status

Windows Admin Center is currently accessible via LAN.

Remote access through Tailscale is not currently functional because Tailscale is not connected/configured on CorpDC.

### Planned Actions

* Install or reconnect Tailscale on CorpDC.
* Verify Tailscale connectivity from Admin Laptop.
* Test Windows Admin Center using CorpDC Tailscale IP.
* Create permanent firewall rules if required.

## Homepage Retirement Plan

Homepage remains installed but is planned for decommissioning.

### Reasons for Migration

* Excessive YAML configuration requirements.
* Higher maintenance burden.
* Less intuitive management workflow.
* Homarr provides a GUI-driven management experience.
* Better long-term scalability for homelab growth.

### Retirement Criteria

Homepage will be removed after:

1. All services have been recreated in Homarr.
2. Dashboard layout is finalized.
3. Management URLs have been verified.
4. Remote access testing is completed.
5. Homarr is designated as the primary browser homepage.

______________________________________________________________________________________________________________
# June 4, 2026

## Summary

Several issues were discovered and resolved prior to leaving for vacation. These included backup interruptions caused by a power outage, deployment of UPS protection, PBS1 recovery, and troubleshooting a Tailscale failure on CorpDC.

---

## Infrastructure Changes

### UPS Deployment

Installed UPS protection for critical infrastructure.

Protected devices:

* PowerEdge T320 (Proxmox Host)
* PowerEdge T320 (CorpDC / Windows Server 2025)
* PBS1 (OptiPlex Proxmox Backup Server)
* Network switch
* Xfinity gateway/router

Purpose:

* Prevent backup interruptions during short power outages.
* Maintain remote access during brief utility interruptions.
* Protect infrastructure from unexpected shutdowns.

---

## CorpDC Tailscale Failure

### Symptoms

CorpDC disappeared from the Tailscale dashboard.

Observed behavior:

```powershell
tailscale status
```

Returned:

```text
Tailscale is starting. Please wait.
unexpected state: NoState
```

Additional behavior:

```text
You are logged out.
The last login error was:
fetch control key: Get "https://controlplane.tailscale.com/key?v=138": context canceled
```

The server had:

* Working internet connectivity
* Working DNS resolution
* Correct time synchronization
* Running Tailscale service

Despite this, Tailscale never obtained an IP address.

---

### Troubleshooting Performed

Verified:

* Internet connectivity via ping
* DNS functionality via nslookup
* NTP synchronization
* Tailscale service status
* Tailscale adapter status
* Control plane connectivity
* Tailscale authentication

Results:

* Network healthy
* DNS healthy
* AD healthy
* DHCP healthy
* Tailscale service running
* Tailscale tunnel adapter present

Issue isolated to local Tailscale installation/state.

---

### Resolution

Removed Tailscale from CorpDC using Windows Admin Center.

Observed:

* Tailscale service removed successfully.
* Uninstall process temporarily hung.
* Windows Installer (msiexec) eventually completed.
* Rebooted CorpDC.

Installed fresh copy of Tailscale.

Verification:

```powershell
tailscale status
tailscale ip -4
```

Successful result:

```text
corpdc
100.120.40.4
```

Node rejoined tailnet successfully.

---

### Final State

Tailscale operational.

CorpDC details:

* Hostname: corpdc
* LAN IP: 10.0.0.45
* Tailscale IP: 100.120.40.4

Confirmed:

* Windows Admin Center accessible
* Tailscale operational
* DNS operational
* DHCP operational
* Active Directory operational

Services verified:

```powershell
Get-Service DNS,DHCPServer,NTDS
```

All services running.

---

## Admin Laptop Changes

### Hostname Change

Changed Ubuntu admin laptop hostname.

Old hostname:

* darknet

New hostname:

* AdminLaptop

Commands used:

```bash
sudo hostnamectl set-hostname AdminLaptop
```

Updated:

```text
/etc/hosts
```

Reboot completed successfully.

---

### Brave Browser Issue

After hostname change, Brave browser reported:

```text
Profile appears to be in use by another Brave process on another computer.
```

Issue determined to be profile locking related to Chromium profile state.

No additional infrastructure impact observed.

---

## Backup Status

### VM102 (Ubuntu.Server)

Backup target:

* optiplex-pbs-4tb

Protected disks:

* 500 GB system disk
* 2 TB data disk
* 2 TB data disk

Total protected size:

* Approximately 4.1 TiB

Observations:

* Backup resumed successfully after previous interruption.
* Dirty bitmaps were invalidated and rebuilt.
* Full scan required.
* No backup errors observed.

Progress at last check:

* 51%
* 7 hours 2 minutes elapsed

Estimated completion:

* Approximately 14 hours total

---

## Pre-Vacation Status

Verified operational:

* CorpDC
* Proxmox Host
* UbuntuServer1
* PBS1
* PBS2
* Tailscale
* DNS
* DHCP
* Active Directory

Remote access methods available:

* Tailscale direct to CorpDC
* Tailscale direct to UbuntuServer1
* SSH to UbuntuServer1
* Windows Admin Center
* RDP to CorpDC

Fallback remote access path:

Laptop
→ Tailscale
→ UbuntuServer1
→ CorpDC (LAN access)

This provides continued management access even if CorpDC Tailscale fails in the future.

---

## Lessons Learned

1. Critical infrastructure should be protected by UPS.
2. Multiple Tailscale-connected devices provide redundancy.
3. Hidden failures (such as Tailscale NoState) may go unnoticed until remote access is needed.
4. Validate remote access before extended travel.
5. Maintain change documentation after major infrastructure modifications.


### Planned Removal Procedure

1. Verify Homarr configuration backup.
2. Stop Homepage container.
3. Confirm no services depend on Homepage configuration.
4. Remove Homepage Docker container.
5. Archive Homepage configuration files.
6. Update homelab documentation.

## Current State

Status: In Progress

### Operational

* Homarr
* Windows Admin Center
* Webmin
* Proxmox
* Wazuh
* Grafana
* Jellyfin
* Plex

### Pending

* Complete Homarr dashboard population.
* Configure CorpDC Tailscale connectivity.
* Validate remote Windows Admin Center access.
* Remove Homepage deployment.
