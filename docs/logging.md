
# Commit 3: Loki + Promtail - Centralized Logging

## 1. Components
- Loki: stores and queries container logs.
- Promtail: collects Docker container logs and sends them to Loki.
- Grafana: queries logs from Loki using LogQL.

## 2. Verified LogQL Queries

### Query 1: All Docker container logs
```logql
{container=~".+"}
```

### Query 2: WordPress-related container logs
```logql
{container=~".*wordpress.*"}
```

### Query 3: Logs containing the keyword error
```logql
{container=~".+"} |= "error"
```

## 3. Results
- Loki and Promtail containers are running.
- Grafana successfully displays log entries and log volume.
- All three LogQL queries were tested in Grafana Explore.
- The third query matches lines containing the word error; matches do not necessarily indicate actual system failures.

## 4. Configuration
- Loki: loki/loki-config.yml
- Promtail: promtail/promtail-config.yml
- Docker services: compose.yaml
