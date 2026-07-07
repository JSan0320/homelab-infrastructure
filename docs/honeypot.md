# Honeypot Environment

## Overview

A dedicated isolated honeypot environment has been deployed to safely collect internet-facing attack traffic and observe attacker behavior without risking production infrastructure.

The honeypot operates independently on a segregated network with complete isolation from production systems.

---

## Network Architecture

### Honeypot Network

**Network Subnet:** `10.0.50.0/24`

**SSID:** `Servers`

**Purpose:**

- Internet-exposed attack collection
- Malware observation
- Credential harvesting
- Command logging
- Threat intelligence collection

This network is intentionally isolated from the production homelab (`10.0.0.0/24`). Only specifically permitted traffic is allowed between networks.

### Firewall Policies

The FortiGate 50E enforces strict firewall policies:

- Honeypot network (`10.0.50.0/24`) cannot initiate connections to production (`10.0.0.0/24`)
- Production network cannot access honeypot services
- Honeypot has unrestricted internet access (inbound from internet, outbound to logging)
- Inter-subnet traffic is explicitly denied by default

---

## Honeypot Hardware

### Beelink Mini S

**Location:** Dedicated physical system

**Operating System:** Ubuntu Server

**Purpose:** Runs internet-facing deception services independent from production infrastructure

**Key Properties:**

- Not virtualized — dedicated physical hardware
- Connected only to the Honeypot network (`10.0.50.0/24`)
- Single-purpose system (no production services)
- Wazuh agent for centralized monitoring
- System firewall (UFW) restricts access to honeypot services only

---

## Deployed Services

### Cowrie SSH Honeypot

**Purpose:** Simulate a Linux SSH server to attract and record attacker activity

**Listening Port:** 2222 (redirected from standard SSH port 22)

**Capabilities:**

- Fake Linux shell and filesystem
- Records all username/password attempts
- Records all executed commands
- Records full SSH sessions with replay support
- Records SSH client fingerprints (HASSH)
- Records file download attempts
- Records malware upload attempts

**Log Files:**

- `/var/log/cowrie/cowrie.log` — Plain text event log
- `/var/log/cowrie/cowrie.json` — Structured JSON events

**Custom Configuration:**

- Configured to emulate a realistic Linux environment
- Fake filesystem mimics actual Linux directory structure
- Support for common Linux tools and utilities in the fake shell

### OpenCanary Multi-Service Honeypot

**Purpose:** Expose additional fake services to attract automated scanners and bots before they reach Cowrie

**Currently Enabled Services:**

- **FTP** (21) — Fake file transfer protocol
- **HTTP** (80) — Fake web server
- **Telnet** (23) — Legacy remote access protocol
- **MySQL** (3306) — Fake database service
- **Redis** (6379) — Fake in-memory database
- **MSSQL** (1433) — Fake Microsoft SQL Server

**Log File:** `/var/tmp/opencanary.log`

**Systemd Service:** `opencanary.service`

**Service Configuration:**

- Automatic startup on system boot
- Automatic restart on failure
- Persistent operation through reboots
- Monitored by Wazuh agent for anomalies

---

## Local Firewall Configuration

### UFW (Uncomplicated Firewall)

The Beelink local firewall is configured to allow only honeypot services and administrative access.

**Allowed Ports:**

- 22 — SSH administration (restricted to internal network)
- 2222 — Cowrie SSH honeypot
- 23 — Telnet (OpenCanary)
- 21 — FTP (OpenCanary)
- 80 — HTTP (OpenCanary)
- 443 — HTTPS (OpenCanary, if configured)
- 3306 — MySQL (OpenCanary)
- 6379 — Redis (OpenCanary)
- 1433 — MSSQL (OpenCanary)

**Key Security Properties:**

- Even though ports are reachable from the internet, they terminate **only** inside Cowrie/OpenCanary
- No direct shell access to Ubuntu itself
- All interaction is contained within honeypot services
- Failed login attempts cannot grant system access
- Downloaded files are isolated within honeypot logs

---

## Wazuh Integration

### Wazuh Agent Deployment

The Beelink runs a dedicated Wazuh Agent that reports to the Wazuh Manager (running on UbuntuServer1).

### Monitored Log Sources

| Source | Path | Purpose |
|---|---|---|
| Cowrie Events | `/var/log/cowrie/cowrie.log` | Plain text event logs |
| Cowrie Structured | `/var/log/cowrie/cowrie.json` | JSON structured events |
| OpenCanary Events | `/var/tmp/opencanary.log` | Service probe logs |
| System Journal | `journald` | System-level events |

### Custom Detection Rules

Custom Wazuh rules have been developed to improve alert readability and enrich descriptions with relevant context.

**Alert Enrichment Includes:**

- Source IP address
- Username and password attempts
- Session ID
- Commands executed
- SSH client fingerprints (HASSH)
- Download URLs (when applicable)
- SHA256 file hashes (when applicable)
- Relevant context from the attack

### Cowrie Alert Categories

Current alert rules cover:

- **Login Success** — Fake credentials accepted
- **Login Failure** — Invalid login attempts
- **Commands Executed** — Commands run in fake shell
- **SSH Fingerprints** — HASSH client fingerprints
- **Malware Downloads** — Files retrieved by attackers
- **Generic Session Events** — General Cowrie activity

### OpenCanary Alert Categories

Current alert rules cover:

- **Generic OpenCanary Events** — Service access attempts
- **HTTP Probes** — Web server reconnaissance
- **FTP Probes** — File transfer reconnaissance
- **Database Probes** — MySQL/MSSQL connection attempts
- **Additional Service-Specific Probes** — Planned expansion

---

## Validation and Testing

### Verification Performed

The honeypot environment has been tested and validated:

- ✓ Cowrie receives real internet attackers
- ✓ Attack sessions appear in Wazuh immediately
- ✓ Credentials are recorded and visible in Wazuh alerts
- ✓ Commands executed are logged and displayed
- ✓ SSH fingerprints are captured and analyzed
- ✓ OpenCanary services appear open during Nmap scans
- ✓ OpenCanary service persists through system reboot
- ✓ Wazuh agent successfully ingests OpenCanary events
- ✓ Wazuh dashboard displays real-time Cowrie activity
- ✓ Wazuh dashboard displays real-time OpenCanary activity
- ✓ Alert descriptions include relevant context

---

## Security Model

### Design Principles

The honeypot is designed with multiple layers of isolation:

| Layer | Control | Purpose |
|---|---|---|
| **Network** | Dedicated subnet (10.0.50.0/24) | Physical network segmentation |
| **Wireless** | Separate SSID (`Servers`) | No production SSID access |
| **Hardware** | Dedicated physical system | No hypervisor compromise risk |
| **Firewall** | Inter-subnet policies | FortiGate blocks inbound from honeypot |
| **Service** | Cowrie/OpenCanary isolation | Contained fake environment |
| **System** | UFW allows honeypot ports only | No system shell access |

### Protection Guarantees

**Attacker Isolation:**

- Attackers connecting to Cowrie interact with fake filesystem and shell
- No commands execute on the real Ubuntu system
- No system shells are granted, even with correct credentials
- All interaction is logged and monitored

**Production Protection:**

- Honeypot cannot reach production infrastructure
- Firewall blocks compromised honeypot from accessing production
- Production systems cannot accidentally connect to honeypot
- Complete network isolation prevents lateral movement

**Data Collection:**

- All attacker behavior is logged and visible in Wazuh
- Attack patterns, TTPs, and tools are recorded
- Threat intelligence is centrally available for analysis

---

## Monitoring and Analysis

### Real-Time Monitoring

Honeypot activity is monitored in real-time through:

- **Wazuh Dashboard** — Live alerts and activity visualization
- **Wazuh API** — Programmatic access to events
- **Grafana Integration** — Custom dashboard panels (future expansion)

### Historical Analysis

All honeypot events are stored in Wazuh for:

- Threat pattern analysis
- Attacker technique identification
- Malware family tracking
- Campaign correlation

### Alert Flow

```
Attacker Activity
        ↓
Cowrie / OpenCanary
        ↓
Agent Log File
        ↓
Wazuh Agent
        ↓
Wazuh Manager (UbuntuServer1)
        ↓
Wazuh Dashboard
        ↓
Analysis / Threat Intelligence
```

---

## Current Architecture Summary

```
Internet
    │
    ↓
FortiGate 50E (10.0.0.1)
    │
    ├─ INFRA-PROD-NET (10.0.0.0/24) ← Production Infrastructure
    │
    ├─ IoT_Devices (10.0.20.0/24)
    │
    ├─ Camera_Devices (10.0.30.0/24)
    │
    ├─ Media_Devices (10.0.40.0/24)
    │
    ├─ Servers / Honeypot (10.0.50.0/24)
    │   │
    │   └─ Beelink Mini S (Ubuntu Server)
    │       │
    │       ├─ Cowrie (SSH 2222)
    │       │   ├─ Fake Shell
    │       │   ├─ Fake Filesystem
    │       │   └─ Session Logging
    │       │
    │       ├─ OpenCanary (FTP, HTTP, Telnet, MySQL, Redis, MSSQL)
    │       │   └─ Service Emulation
    │       │
    │       └─ Wazuh Agent
    │           └─ Event Reporting
    │
    ├─ Guest_Devices (10.0.60.0/24)
    │
    └─ [Firewall: No production access from honeypot]

UbuntuServer1 (10.0.0.42)
    │
    └─ Wazuh Manager
        │
        └─ Alert Processing & Storage
            │
            └─ Wazuh Dashboard
```

---

## Operational Notes

### System Monitoring

Monitor honeypot health through:

- Wazuh agent status in Wazuh dashboard
- Service status: `systemctl status opencanary.service`
- Cowrie logs: `tail -f /var/log/cowrie/cowrie.json`
- OpenCanary logs: `tail -f /var/tmp/opencanary.log`

### Maintenance

Regular maintenance tasks:

- Monitor disk usage for logs
- Verify Wazuh agent connectivity
- Review threat intelligence insights
- Update honeypot service configurations as needed
- Plan log rotation to prevent disk exhaustion

### Future Enhancements

Potential expansions:

- Additional honeypot services (RDP, SMB, etc.)
- Enhanced Cowrie emulation (additional command support)
- Custom malware analysis integration
- Automated YARA rule generation from captured samples
- Integration with external threat intelligence platforms

---

## References

- **Cowrie Project:** https://github.com/cowrie/cowrie
- **OpenCanary Project:** https://github.com/thinkst/opencanary
- **Wazuh Security Monitoring:** https://wazuh.com/
- **FortiGate Firewall:** https://www.fortinet.com/
