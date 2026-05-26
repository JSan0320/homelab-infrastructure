# Backups and Disaster Recovery

## Overview

The homelab uses a layered backup and disaster recovery strategy designed to provide:

- local VM recovery
- offsite disaster recovery
- Windows infrastructure recovery
- configuration protection
- backup redundancy
- remote restoration capability

The environment combines:
- Proxmox Backup Server
- offsite replication
- Windows System State backups
- Samba network storage
- future warm-site planning

---

# Backup Architecture

## Virtual Machine Backup Flow

```text
Proxmox VE
    ↓
PBS1 Local Backup Server
    ↓
PBS2 Offsite Backup Server
```

All critical Proxmox VMs are backed up through PBS.

---

## Windows Server Backup Flow

The physical Windows Server domain controller uses Windows System State Backup.

The backup chain is:

```text
CorpDC
↓
Windows System State Backup
↓
Samba Share on UbuntuServer1
↓
PBS1 Backup
↓
PBS2 Offsite Sync
```

This protects:
- Active Directory
- DNS
- DHCP
- Group Policy
- registry
- system configuration

---

# Proxmox Backup Infrastructure

## PBS1

| Item | Details |
|---|---|
| Role | Local backup server |
| Hardware | Dell OptiPlex |
| IP | 10.0.0.30 |
| Storage | Internal + external backup storage |
| Purpose | Primary VM backup target |

---

## PBS2

| Item | Details |
|---|---|
| Role | Offsite disaster recovery backup server |
| Location | Remote/offsite site |
| Connectivity | Tailscale |
| Purpose | Offsite backup replication |

---

# Dedicated Backup Network

A dedicated backup network exists between:
- Proxmox VE host
- PBS1

## Backup Subnet

| Device | IP |
|---|---|
| Proxmox Backup NIC | 192.168.50.1 |
| PBS1 Backup NIC | 192.168.50.2 |

Purpose:
- isolated backup traffic
- improved transfer performance
- reduced LAN congestion

---

# Protected Systems

| System | Protection Method |
|---|---|
| UbuntuServer1 | PBS VM backup |
| AI Assistant VM | PBS VM backup |
| CorpDC | Windows System State backup to Samba |
| Monitoring stack | Included within UbuntuServer1 |
| Samba shares | Included within UbuntuServer1 backups |

---

# Windows Infrastructure Protection

The Windows Server 2025 domain controller is a physical machine and is not virtualized.

Because of this, recovery uses:
- Windows System State Backup
- Samba backup storage
- PBS protection through UbuntuServer1 backups

## Protected Windows Components

The backup includes:
- Active Directory
- DNS configuration
- DHCP configuration
- SYSVOL
- Group Policy
- registry
- boot configuration
- Windows infrastructure metadata

---

# Offsite Disaster Recovery

PBS1 synchronizes backups to PBS2 using:
- scheduled sync jobs
- Tailscale remote connectivity

This protects against:
- hardware failure
- accidental deletion
- failed updates
- ransomware
- site-level disaster

---

# Recovery Scenarios

## VM Failure

Recovery method:
- restore VM directly from PBS

Typical recovery targets:
- UbuntuServer1
- AI Assistant VM

---

## CorpDC Failure

Recovery method:
- reinstall Windows Server
- restore System State backup

This restores:
- Active Directory
- DNS
- DHCP
- Group Policy
- domain configuration

---

## Full Site Loss

Recovery method:
- restore VMs from PBS2 at offsite location

Future goal:
- warm-site Proxmox recovery environment

---

# Warm Site Planning

The offsite location currently contains:
- PBS2
- OpenWrt router
- Tailscale connectivity

Future planned additions:
- small Proxmox host
- restore-ready infrastructure
- rapid VM recovery capability

This would allow:
- UbuntuServer1 restoration
- AI Assistant restoration
- monitoring recovery
- Samba recovery
- infrastructure recovery at the offsite location

---

# Retention Strategy

Current PBS retention includes:
- daily backups
- weekly backups
- monthly backups
- yearly backups

This allows:
- rollback capability
- corruption recovery
- accidental deletion recovery
- long-term recovery points

---

# Operational Goals

The backup architecture is designed to provide:

- reliable VM recovery
- offsite redundancy
- disaster recovery capability
- minimal infrastructure downtime
- backup visibility
- centralized backup management

---

# Future Improvements

Planned improvements include:

- automated backup validation
- restore testing workflows
- AI backup alerting
- PBS datastore usage monitoring
- backup health dashboards
- warm-site VM restoration
- automated disaster recovery testing
