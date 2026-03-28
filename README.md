# Lightweight Suricata Dashboard

This project provides a lightweight observability stack for Suricata EVE logs using Loki, Promtail, and Grafana.
It is designed to run with Docker Compose and automatically provisions both the Loki datasource and the Suricata dashboard.

## Solution Overview

The stack has three services:

1. Promtail
	- Reads Suricata logs from /var/log/suricata/eve.json on the host.
	- Parses key JSON fields like event_type, src_ip, and dest_ip.
	- Enriches src_ip and dest_ip with GeoIP data via GeoLite2.
	- Pushes labeled log streams to Loki.

2. Loki
	- Stores indexed log streams and powers LogQL queries used by Grafana.
	- Uses local filesystem-backed storage under loki/data.

3. Grafana
	- Auto-loads a Loki datasource and the Suricata dashboard from provisioning files.
	- Visualizes alerts, DNS/HTTP activity, top IPs and ports, and a world geo map.

## Data Flow

Suricata EVE JSON -> Promtail pipeline (parse + GeoIP + labels) -> Loki -> Grafana dashboard panels

## Included Dashboard Coverage

The provisioned Suricata dashboard includes:

- High-level volume stats (alerts, DNS, HTTP, flow)
- Alert rate and severity distribution
- Top alert signatures, source IPs, destination IPs, and destination ports
- DNS and HTTP analysis panels
- Live alert log stream
- GeoIP world map using latitude/longitude labels

## Repository Layout

- docker-compose.yml: Service orchestration for Loki, Promtail, and Grafana
- loki/config.yaml: Loki storage, ingestion, and query limits
- promtail/promtail-config.yml: Suricata scrape config and enrichment pipeline
- grafana/provisioning/datasources/loki.yml: Auto-provisioned Loki datasource
- grafana/provisioning/dashboards/dashboard.yaml: Dashboard provider config
- grafana/provisioning/dashboards/suricata.json: Main dashboard definition

## Quick Start

1. Ensure Suricata writes EVE logs to /var/log/suricata/eve.json on the host.
2. Ensure GeoLite DB exists at promtail/geoip/GeoLite2-City.mmdb.
3. Start the stack:

```bash
docker compose up -d
```

4. Open Grafana at http://localhost:3000
	- Username: admin
	- Password: admin

## Notes

- Grafana, datasource, and dashboard are provisioned from files and reload automatically.
- Loki data is persisted in the local project directory at loki/data.
- This setup is intended for single-node, local or small deployment scenarios.
