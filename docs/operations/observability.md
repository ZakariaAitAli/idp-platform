# Observability Model

## Stack
- Prometheus for metrics collection and alerting.
- Loki for log aggregation.
- Grafana for dashboards and multi-tenant visualization.

## Tenancy
- Shared platform-level stack; no per-team forks.
- Separation via namespace-based scrape configs and log labels; Grafana folder/role assignments restrict dashboard access.
- Alert routing is centralized; team ownership is expressed through labels (service, team, severity).

## Data Flow
- Metrics: Scraped via service discovery; workloads opt in with standard annotations (e.g., `prometheus.io/scrape: "true"`, `prometheus.io/port`). Remote-write targets can be added via configuration if needed.
- Logs: Shipped to Loki via agents (not included here; expected to be DaemonSets such as promtail or OpenTelemetry collectors defined in infra layer). Namespace labels are preserved for multi-tenancy.
- Dashboards and data sources: Provisioned via ConfigMaps/Secrets delivered through GitOps and ExternalSecrets; no UI-based persistence.

## Access
- Grafana admin credentials via ExternalSecret (`kubernetes/observability/externalsecret-grafana.yaml`); rotate in Vault.
- SSO with Keycloak recommended for user access; configure in Vault-managed Grafana config.

## Reliability Expectations
- Stack deployed and reconciled by Argo CD; manual changes are overwritten.
- Retention, storage classes, and backends must be set per environment (local PVs vs. EKS storage/S3-compatible object storage for Loki/Prometheus).
- Health is measured via scrape errors, ingestion latency, and dashboard availability; alerts should be defined accordingly.
