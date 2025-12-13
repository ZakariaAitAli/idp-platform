# Templates and Golden Paths

## Purpose
Workload teams must onboard through approved templates to ensure compliance with platform contracts (namespaces, identity, networking, secrets).

## Service Template
- Location: `templates/workload-service/`
- Contents: Namespace, ServiceAccount, Deployment, Service, ExternalSecret, NetworkPolicy, and Kustomization.
- Placeholders: `APP_NAME`, `YOUR_NAMESPACE`, `TEAM_NAME`, and container image/tag.
- Secrets: ExternalSecret points to `workloads/APP_NAME/config` in Vault; platform team must provision the path and policy.
- Networking: NetworkPolicy allows ingress only from ingress-nginx on HTTP port; extend explicitly if services need pod-to-pod access.
- Identity: ServiceAccount is per app; tie it to a namespace-scoped Vault role.
- Resources: Deployment includes baseline requests/limits; adjust via PR to the workload repo.

## Usage
1. Copy template into workload repo under `gitops/<env>/workloads/<app>`.
2. Replace placeholders and set image/tag.
3. Commit and let Argo CD reconcile; do not apply manually.
4. Use namespace-scoped ExternalSecrets only; do not create or modify SecretStores.

## Enforcement
- Workload repos are expected to be restricted to these templates via code review and policy (e.g., OPA/Conftest if desired).
- Drift from templates is disallowed unless explicitly approved by platform team.
