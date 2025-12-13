# Identity and Access

## Human Access
- Keycloak is the identity provider for SSO. MFA is required where supported.
- Argo CD integrates with Keycloak for RBAC mapping to `platform-admins` and `platform-readers`; projects can map additional groups for workload owners.
- Cluster kubectl access is federated through Keycloak-issued tokens; local admin kubeconfigs are restricted to break-glass use with time bounds and logging.
- Access reviews are performed regularly; dormant accounts are disabled.

## Workload Identity
- Workloads use Kubernetes ServiceAccounts only. No static credentials are embedded in images.
- Vault Kubernetes auth maps ServiceAccounts to Vault roles with namespace-scoped policies.
- ExternalSecrets authenticate with a dedicated ServiceAccount (`external-secrets` in `external-secrets` namespace) and a Vault role `external-secrets`.
- IRSA (EKS): ServiceAccounts annotated with IAM roles when workloads/controllers require AWS APIs; roles are least-privilege and namespace-scoped.

## RBAC Boundaries
- Platform team owns platform namespaces, CRDs, and controller configurations.
- Workload teams own only their namespaces and resources created via platform templates.
- Cross-namespace access is denied by default; NetworkPolicies restrict ingress/egress to approved paths (e.g., ingress-nginx).
- Argo CD Projects restrict destinations and cluster-scoped resources per team.

## Audit
- Keycloak audit logs, Kubernetes API audit logs, Vault audit logs, and Argo CD history provide traceability for all access patterns.
- Periodic reconciliation checks ensure RBAC matches declared policy; drift is treated as an incident.
