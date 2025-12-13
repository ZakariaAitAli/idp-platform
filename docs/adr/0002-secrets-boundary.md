# ADR 0002: Vault as Source of Truth with External Secrets Operator

## Status
Accepted

## Context
Secrets must never reside in Git or be distributed through ad-hoc channels. Workloads need Kubernetes-native access without managing Vault clients.

## Decision
- Vault is the single source of secret truth.
- External Secrets Operator authenticates to Vault via Kubernetes auth and materializes secrets as Kubernetes Secrets.
- Workloads may only consume Kubernetes Secrets within their namespace. They cannot contact Vault directly.
- Vault policies and auth roles are managed by the platform team; workload teams receive only scoped paths via ExternalSecrets.

## Consequences
- Secret sprawl is prevented; auditability remains in Vault.
- Workload manifests stay environment-agnostic; Vault paths map to overlays.
- Secret rotation is centralized and non-disruptive to workloads.
