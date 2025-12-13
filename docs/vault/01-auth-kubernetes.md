# Kubernetes Authentication Flow

## Objective
Provide identity-bound, short-lived Vault tokens for platform controllers (ESO) and, if ever required, tightly scoped operational tooling—never for applications.

## Authentication Flow
1. A Pod runs with a dedicated ServiceAccount; `automountServiceAccountToken` is enabled only for Pods that must talk to Vault (ESO).
2. Kubernetes projects a signed JWT into the Pod.
3. Vault validates the JWT issuer and `aud` against the Kubernetes API server (or EKS OIDC provider when migrated).
4. Vault maps the identity to a Vault role. Roles include namespace restrictions and bound ServiceAccounts.
5. Vault issues a short-lived token bound to policies; ESO refreshes before expiry.

## Strategy
- One Vault Kubernetes auth mount per cluster (`kubernetes/`).
- Roles are namespace-scoped: `role = <namespace>-eso` or per workload if stricter blast radius is needed.
- TTLs are short (minutes) with renewals allowed; tokens are non-root and non-transitive.
- Only controllers (ESO) are allowed to authenticate; application Pods are explicitly forbidden from holding Vault tokens.

## Required Kubernetes Objects
- ServiceAccount used for identity (per namespace or per controller).
- ClusterRoleBinding granting `system:auth-delegator` to the Vault auth ServiceAccount (only).

## Required Vault Objects
- Kubernetes auth method configured with cluster CA, API host, and OIDC issuer.
- Vault role binding ServiceAccount(s) and namespace(s) to specific policies.
- Policies themselves granting only `read`/`list` for required paths.

Authentication proves identity; authorization is enforced exclusively by policies.
