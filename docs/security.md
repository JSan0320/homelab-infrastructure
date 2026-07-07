# Security Architecture

## Overview

The homelab includes multiple security-focused systems and design principles intended to provide:

- network visibility
- centralized monitoring
- segmentation
- endpoint visibility
- logging
- remote access security
- backup resilience
- infrastructure hardening

The environment is used to practice:
- security operations concepts
- infrastructure security
- detection engineering
- monitoring workflows
- network visibility
- disaster recovery planning

---

# Security Design Goals

The lab is designed around several core principles:

- layered visibility
- centralized monitoring
- secure remote access
- backup redundancy
- segmentation
- observability
- infrastructure resilience

---

# Core Security Components

| Component | Purpose |
||---|---|
|| FortiGate 50E | Primary edge firewall, gateway, and wireless controller |
|| Wazuh | SIEM and security monitoring (includes honeypot events) |
|| Bettercap IDS | Network traffic visibility |
|| Raspberry Pi IDS | Passive monitoring system |
|| Pi-hole | DNS visibility and filtering |
|| VLANs | Network segmentation |
|| Tailscale | Secure remote connectivity |
|| PBS Offsite Replication | Disaster recovery protection |
|| Grafana/Loki | Monitoring and log visibility |
|| Telegram AI Assistant | Operational awareness |
|| Honeypot Environment | Isolated attack collection (Cowrie + OpenCanary) |

---

# Wazuh SIEM

## Purpose

Wazuh is used for:
- centralized monitoring
- endpoint visibility
- event collection
- security experimentation
- operational monitoring

## Monitored Systems

| System | Monitoring Status |
|---|---|
| UbuntuServer1 | Monitored |
| Proxmox VE Host | Monitored |
| Windows Server 2025 | Monitored |

## Security Visibility Areas

- authentication events
- service changes
- infrastructure events
- suspicious activity
- endpoint visibility
- system monitoring

## Learning Goals

The Wazuh deployment is used to learn:
- SIEM architecture
- log analysis
- detection concepts
- operational visibility
- infrastructure monitoring

---

# Network Monitoring

## Raspberry Pi IDS

The Raspberry Pi IDS provides:
- passive monitoring
- traffic visibility
- DNS visibility
- packet inspection
- traffic analysis

## Tools Used

- Bettercap
- switch port mirroring
- traffic inspection
- DNS monitoring

## Port Mirroring

The managed switch mirrors traffic to the IDS device.

This allows:
- network visibility
- passive analysis
- security experimentation
- troubleshooting visibility

---

# DNS Security

## Pi-hole

Pi-hole provides:
- DNS filtering
- ad blocking
- DNS visibility
- request logging

## Benefits

- visibility into DNS activity
- malicious domain blocking potential
- centralized DNS analysis
- reduced unwanted traffic

---

# Network Segmentation

The network uses VLAN segmentation for:
- guest isolation
- management separation
- traffic organization
- monitoring visibility

## VLAN Concepts

| VLAN Purpose | Description |
|---|---|
| Main LAN | Trusted infrastructure |
| Guest VLAN | Isolated guest wireless devices |
| Management | Administrative systems |
| Monitoring | IDS and monitoring visibility |

---

# Remote Access Security

## Tailscale

Remote access is handled through:
- Tailscale overlay networking

Benefits:
- encrypted remote connectivity
- reduced port exposure
- secure offsite access
- simplified remote management

## Remote Access Goals

- avoid unnecessary public exposure
- secure remote administration
- encrypted connectivity
- protected offsite management

---

# Backup and Recovery Security

## Multi-Layer Backup Strategy

The environment uses:
- local PBS backups
- offsite PBS replication
- Windows System State backups
- disaster recovery planning

## Security Benefits

This helps protect against:
- ransomware
- accidental deletion
- hardware failure
- corruption
- failed updates
- site-level disaster

---

# Logging and Observability

## Centralized Visibility

The monitoring stack includes:
- Grafana
- Prometheus
- Loki
- Wazuh
- goflow2

This provides:
- infrastructure visibility
- historical logs
- troubleshooting data
- operational awareness
- security visibility

---

# Honeypot Security Environment

## Overview

A dedicated, isolated honeypot environment collects internet-facing attack traffic safely without exposing production systems.

## Architecture

**Network:** `10.0.50.0/24` (completely isolated from production `10.0.0.0/24`)

**Hardware:** Beelink Mini S (dedicated physical system, not virtualized)

**Services:**

- **Cowrie** — SSH honeypot on port 2222
  - Captures fake shell sessions
  - Records usernames, passwords, commands
  - Collects SSH fingerprints (HASSH)
  - Captures malware uploads

- **OpenCanary** — Multi-service deception
  - FTP, HTTP, Telnet, MySQL, Redis, MSSQL
  - Attracts automated scanners before Cowrie
  - Logs all connection attempts

## Security Isolation

| Layer | Control |
|---|---|
| **Network** | Dedicated subnet, no production access from honeypot |
| **Wireless** | Separate SSID (`Servers`), not on production network |
| **Hardware** | Dedicated physical system, no hypervisor compromise risk |
| **Firewall** | FortiGate blocks inbound from honeypot to production |
| **Services** | Cowrie/OpenCanary are completely contained |
| **System** | UFW allows only honeypot ports; no system shell access |

## Wazuh Integration

The Beelink Wazuh Agent reports all honeypot activity to the Wazuh Manager:

**Monitored Sources:**
- Cowrie logs (`/var/log/cowrie/cowrie.json`)
- OpenCanary logs (`/var/tmp/opencanary.log`)
- System journal

**Custom Alert Rules:**
- Login attempts (success/failure)
- Commands executed in fake shell
- SSH client fingerprints
- File downloads/uploads
- Service probe detection

**Real-Time Visibility:**
- Live honeypot activity in Wazuh dashboard
- Attack pattern analysis
- Threat intelligence collection
- Automated alert generation

## Benefits

- **Safe Threat Collection** — Observe attacker behavior in isolated environment
- **Production Protection** — Compromised honeypot cannot reach production systems
- **Threat Intelligence** — Capture TTPs, tools, malware samples
- **Detection Tuning** — Use real-world attack patterns for alert development
- **Operational Experience** — Hands-on incident response practice

---

# AI Operations Assistant

The Telegram AI Assistant contributes to operational security through:
- rapid infrastructure visibility
- backup awareness
- VM visibility
- daily reporting
- remote operational awareness

## Current Security Principles

- read-only monitoring focus
- `.env` secret storage
- no secrets committed to GitHub
- minimal API permissions where possible

---

# Windows Security Infrastructure

The Windows Server 2025 environment provides:
- Active Directory
- centralized authentication
- Group Policy
- internal DNS
- DHCP management

This is used to practice:
- enterprise Windows administration
- identity management
- centralized policy management

---

# Operational Security Practices

Current operational practices include:
- centralized backups
- offsite replication
- infrastructure documentation
- monitoring visibility
- segmented guest access
- remote encrypted management
- infrastructure logging

---

# Future Security Goals

Planned future improvements include:

- enhanced Wazuh detections
- alert severity classification
- automated alerting
- stronger segmentation
- dedicated management VLAN
- SSH hardening
- MFA expansion
- improved IDS visibility
- detection engineering workflows
- centralized alert management
- disaster recovery testing
