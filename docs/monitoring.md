# Monitoring and Observability

## Overview

The homelab uses a centralized monitoring and observability stack designed to provide:

- infrastructure visibility
- performance monitoring
- centralized logging
- security visibility
- network traffic monitoring
- operational alerting
- remote reporting

The environment combines:
- Grafana
- Prometheus
- Loki
- Wazuh
- goflow2
- Bettercap IDS
- Telegram AI reporting

---

# Monitoring Architecture

```text
Infrastructure Devices
        ↓
Exporters / Agents / Logs / Flows
        ↓
Prometheus / Loki / Wazuh / goflow2
        ↓
Grafana Dashboards
        ↓
Telegram AI Reporting
```

---

# Core Monitoring Components

| Tool | Purpose |
|---|---|
| Grafana | Dashboards and visualization |
| Prometheus | Metrics collection |
| Loki | Centralized log aggregation |
| Wazuh | SIEM and security monitoring |
| goflow2 | Network flow collection |
| Bettercap IDS | Traffic monitoring and inspection |
| Telegram AI Bot | Mobile monitoring and reporting |

---

# Grafana

## Purpose

Grafana provides:
- centralized dashboards
- infrastructure visualization
- performance trending
- operational visibility

## Current Uses

- CPU usage monitoring
- RAM usage monitoring
- disk usage monitoring
- VM visibility
- PBS visibility
- Prometheus visualization
- infrastructure health visibility

## Access

| Item | Value |
|---|---|
| Host | UbuntuServer1 |
| Port | 3000 |

---

# Prometheus

## Purpose

Prometheus collects:
- infrastructure metrics
- node metrics
- VM performance data
- exporter data

## Monitored Systems

| System | Exporter |
|---|---|
| UbuntuServer1 | node_exporter |
| Proxmox VE Host | node_exporter |
| PBS1 | node_exporter |
| PBS2 | node_exporter |

## Common Metrics

- CPU usage
- RAM usage
- filesystem usage
- uptime
- load averages
- network statistics

## Access

| Item | Value |
|---|---|
| Host | UbuntuServer1 |
| Port | 9090 |

---

# Loki

## Purpose

Loki provides:
- centralized logging
- log aggregation
- searchable infrastructure logs

## Current Logging Sources

- Linux system logs
- service logs
- infrastructure logs
- operational troubleshooting logs

## Benefits

- centralized troubleshooting
- easier issue investigation
- historical log visibility
- operational visibility

---

# Wazuh

## Purpose

Wazuh provides:
- SIEM functionality
- security monitoring
- endpoint visibility
- alerting
- security event collection

## Monitored Systems

| System | Status |
|---|---|
| UbuntuServer1 | Monitored |
| Proxmox VE Host | Monitored |
| Windows Server 2025 | Monitored |
| Additional systems | Expandable |

## Security Monitoring Areas

- authentication events
- system changes
- suspicious activity
- service monitoring
- agent visibility
- infrastructure monitoring

## Purpose in Homelab

The Wazuh environment is used to learn:
- SIEM concepts
- detection engineering
- log analysis
- infrastructure monitoring
- security operations workflows

---

# goflow2

## Purpose

goflow2 collects:
- NetFlow traffic data
- flow visibility
- traffic analysis metrics

## Current Configuration

| Item | Value |
|---|---|
| Flow Port | 2055 |
| Metrics Port | 8080 |

## Benefits

- traffic visibility
- bandwidth analysis
- flow monitoring
- network usage visibility

---

# Raspberry Pi IDS

## Purpose

The Raspberry Pi IDS provides:
- traffic monitoring
- packet visibility
- network inspection
- security experimentation

## Tools Used

- Bettercap
- port mirroring
- DNS inspection
- traffic analysis

## Network Integration

The managed switch mirrors traffic to the Raspberry Pi IDS.

This allows:
- passive monitoring
- network visibility
- traffic analysis
- IDS experimentation

---

# AI Monitoring Integration

The AI Assistant VM integrates monitoring data into Telegram reports.

## Current Features

| Feature | Status |
|---|---|
| Reachability checks | Implemented |
| VM status reporting | Implemented |
| Proxmox API integration | Implemented |
| Daily reporting | Implemented |
| Backup reporting | Implemented |

## Future Planned Features

- Wazuh alert summaries
- Grafana metric summaries
- PBS datastore monitoring
- offline alerts
- anomaly detection
- operational recommendations

---

# Operational Visibility Goals

The monitoring stack is designed to provide:

- centralized observability
- proactive monitoring
- security visibility
- performance visibility
- backup awareness
- remote operational awareness
- troubleshooting visibility

---

# Future Improvements

Planned future improvements include:

- centralized alerting
- alert severity classification
- Discord/Telegram notifications
- dashboard improvements
- additional exporters
- detection engineering workflows
- IDS rule expansion
- long-term metric retention
- automated reporting improvements
