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
- Grafana Alloy
- Wazuh
- Netdata
- goflow2
- Bettercap IDS
- Telegram AI reporting (Hermes-Agent)

---

# Monitoring Architecture

```text
Infrastructure Devices (Proxmox, UbuntuServer1, PBS1, CorpDC, Pi IDS)
        |
Exporters / Agents / Syslog / NetFlow / IDS Logs
        |
        +---- Prometheus         (node_exporter metrics)
        +---- Grafana Alloy      (log shipping, NetFlow, IDS logs → Loki)
        +---- Wazuh Manager      (security events from 9 agents)
        +---- goflow2            (NetFlow from softflowd on all hosts)
        +---- Netdata            (real-time node metrics → Netdata Cloud)
        |
Grafana Dashboards
        |
Hermes-Agent Telegram Reports (8 AM / 8 PM CDT)
```

---

# Core Monitoring Components

| Tool | Host | Purpose |
|---|---|---|
| Grafana | UbuntuServer1:3000 | Dashboards and visualization |
| Prometheus | UbuntuServer1:9090 | Metrics collection and storage |
| Loki | UbuntuServer1:3100 | Centralized log aggregation |
| Grafana Alloy | UbuntuServer1 | Log/metric pipeline (NetFlow, IDS, syslog) |
| Wazuh Manager | UbuntuServer1:55000 | SIEM and security monitoring |
| Wazuh Dashboard | UbuntuServer1:443 | Security event visualization |
| goflow2 | UbuntuServer1:2055/8080 | NetFlow collection and metrics |
| Netdata | All nodes | Real-time node metrics (claimed to Netdata Cloud) |
| Bettercap IDS | Pi IDS (10.0.0.170) | Passive traffic monitoring |

---

# Grafana

## Purpose

Grafana provides centralized dashboards for infrastructure visualization, performance trending, and operational visibility.

## Access

| Item | Value |
|---|---|
| Host | UbuntuServer1 |
| URL | http://10.0.0.42:3000 |

## Datasources

- Prometheus (default — infrastructure metrics)
- Loki (×2 — system logs and NetFlow/IDS logs)

---

# Prometheus

## Purpose

Prometheus collects infrastructure metrics via node_exporter from all monitored hosts.

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
- uptime and load averages
- network statistics

## Access

| Item | Value |
|---|---|
| URL | http://10.0.0.42:9090 |

---

# Loki

## Purpose

Loki aggregates logs from across the infrastructure via Grafana Alloy pipelines.

## Log Sources

- Linux system logs (all VMs)
- Bettercap IDS logs (Pi → rsync → Alloy → Loki)
- NetFlow data (softflowd → goflow2 → Alloy → Loki)
- Service logs

---

# Grafana Alloy

## Purpose

Grafana Alloy acts as the centralized log and metric pipeline, replacing legacy Promtail deployments.

## Data Flows

| Source | Pipeline |
|---|---|
| IDS logs (Raspberry Pi) | rsync to UbuntuServer1 → Alloy → Loki |
| NetFlow | softflowd → goflow2 → Alloy → Loki |
| System logs | Alloy → Loki |

---

# Wazuh SIEM

## Purpose

Wazuh provides SIEM functionality, endpoint visibility, security event collection, and alerting.

## Access

| Item | Value |
|---|---|
| API | https://10.0.0.42:55000 |
| Dashboard | https://10.0.0.42:443 |

## Monitored Agents (9 active)

| Agent | System |
|---|---|
| ubuntuserver1 | UbuntuServer1 (local) |
| PowerEdge1 | Proxmox VE host |
| WindowsServer1 | CorpDC |
| PBS1 | Proxmox Backup Server 1 |
| AdminLaptop | Admin workstation |
| raspberrypi-ids | Raspberry Pi IDS |
| GamingPC | Gaming PC |
| pbs2 | PBS2 offsite |
| AI.Assistant | VM 103 |

## Security Monitoring Areas

- authentication events
- system changes
- suspicious activity
- service monitoring
- agent visibility
- infrastructure monitoring

---

# goflow2 — NetFlow Collection

## Purpose

goflow2 collects NetFlow data sent from softflowd agents running on all hosts.

## Configuration

| Item | Value |
|---|---|
| Flow input port | 2055 |
| Metrics port | 8080 |

## Benefits

- traffic visibility across all hosts
- bandwidth analysis
- flow monitoring
- network usage trends

---

# Netdata

## Purpose

Netdata provides real-time per-node metrics on all infrastructure hosts.

All nodes are claimed to Netdata Cloud for centralized visibility.

## Covered Nodes

- Proxmox VE Host
- UbuntuServer1
- PBS1
- PBS2

---

# Raspberry Pi IDS

## Purpose

The Raspberry Pi IDS (10.0.0.170) provides passive traffic monitoring via Bettercap and switch port mirroring.

## Tools

| Tool | Function |
|---|---|
| Bettercap `net.recon` | Host discovery |
| Bettercap `net.probe` | Active probing |
| Bettercap `net.sniff` | Packet capture |

## Integration

Logs are rsynced to UbuntuServer1, ingested by Grafana Alloy, and shipped to Loki for Grafana visualization.

---

# AI Monitoring Integration (Hermes-Agent)

Hermes-Agent (Mara) delivers scheduled infrastructure reports to Telegram.

## Implemented Features

| Feature | Status |
|---|---|
| Reachability checks (ping) | ✅ Active |
| VM status and resources (Proxmox API) | ✅ Active |
| Proxmox node metrics (SSH) | ✅ Active |
| Backup status (PBS API + PVE tasks) | ✅ Active |
| PBS datastore usage | ✅ Active |
| Email monitoring and replies | ✅ Active |
| Telegram Telegram relay | ✅ Active |
| Daily reports (8 AM / 8 PM CDT) | ✅ Scheduled |

## Future Planned Features

- Wazuh alert summaries in daily reports
- Grafana anomaly detection integration
- PBS2 offsite sync status
- Automated changelog generation

---

# Future Monitoring Improvements

Planned improvements include:

- centralized alerting with severity classification
- Wazuh detection engineering workflows
- dashboard improvements
- additional node_exporter coverage
- long-term metric retention tuning
- automated anomaly alerting via Telegram
