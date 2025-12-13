# Workload Secret Consumption Contract

## Principles
- Workloads never authenticate to Vault and never carry Vault tokens.
- Workloads consume only Kubernetes Secrets produced by External Secrets Operator (ESO).
- Secret material is ephemeral: if deleted, ESO will recreate it from Vault.

## Responsibilities
- **Platform**: Operates Vault, ESO, and per-namespace SecretStores bound to Vault roles/policies.
- **Workload**: Defines `ExternalSecret` manifests that map Vault paths to Kubernetes Secrets and references them from Pods.
- **Both**: Honor namespace isolation; no cross-namespace SecretStores or shared ServiceAccounts.

## Allowed Pattern
1. Platform team creates a namespaced `SecretStore` wired to Vault and bound to a least-privilege policy.
2. Workload team creates an `ExternalSecret` referencing that `SecretStore` and specific Vault paths.
3. Deployment consumes only the Kubernetes Secret written by ESO (via `secretKeyRef`, `envFrom`, or volume).

## Forbidden Pattern
- Direct Vault API calls, Vault agents, or sidecars in application Pods.
- Embedding static credentials in manifests or ConfigMaps.
- Reusing one `SecretStore` across namespaces.

This template enforces the allowed pattern and blocks the forbidden ones.
