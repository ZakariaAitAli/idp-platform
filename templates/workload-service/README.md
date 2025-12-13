Golden path for stateless HTTP workloads. Use per workload namespace provided by the platform team.

## Usage
- Copy this template into your workload repo under `gitops/<env>/workloads/<app>`.
- Replace placeholders (`APP_NAME`, `YOUR_NAMESPACE`, `TEAM_NAME`) and set container image/tag.
- Define ExternalSecret keys to Vault paths provisioned by the platform team.
- Commit and allow Argo CD to reconcile; no manual kubectl.
