# Platform Architecture

This document describes the target state of the Internal Developer Platform. It is intentionally production-minded and environment-agnostic; all drift between local kubeadm and AWS EKS is explicit and minimized.

## Control Plane Composition

- GitOps: Argo CD is the single reconciler. Every platform component is represented as an Argo CD Application defined in Git. No imperative `kubectl`.
- Secrets: Vault is the root of secret truth. External Secrets Operator (ESO) is the only bridge to Kubernetes Secrets.
- Identity: Keycloak issues identities for humans; workloads use Kubernetes ServiceAccounts and projected identities only.
- Developer Experience: Backstage is the entrypoint for catalog, ownership, and golden paths. It never bypasses platform controls.
- Observability: Prometheus, Loki, and Grafana operate as shared platform services. No per-app one-offs.

## Declarative Flow

1. Desired state lives in Git (`gitops/` for Argo CD Applications; `kubernetes/` for manifests and overlays; `templates/` for golden paths).
2. Argo CD pulls from Git and reconciles into dedicated namespaces per component.
3. Vault stores secrets and policies; it never faces workloads directly.
4. ESO authenticates to Vault via Kubernetes auth, reads only approved paths, and writes scoped Kubernetes Secrets.
5. Workloads consume Kubernetes Secrets and ServiceAccounts; they never embed Vault tokens or static credentials.

## Trust Boundaries

- Platform Administrators: Manage Argo CD Projects/Applications, Vault auth methods, policies, and platform namespaces. Do not modify workload namespaces except for shared controllers.
- Workload Teams: Receive namespaces, quotas, and templates. Author ExternalSecret manifests within their namespace; cannot author Vault policies or SecretStores.
- Argo CD: Cluster-admin scoped to platform namespaces; no access to Vault data.
- External Secrets Operator: Vault policy grants read-only access to specific secret paths; cannot write or administer Vault.
- Workloads: Access limited to Kubernetes Secrets and ServiceAccounts in their namespace; no network path or credentials to Vault.
- Auditors: Read-only Git and Argo CD; Vault audit logs capture every secret read.

## Operational Invariants

- Git is the single source of desired state; drift is corrected automatically.
- Vault is the only source of secret truth; Kubernetes Secrets are ephemeral derivatives.
- Applications never speak to Vault directly.
- One Argo CD Application per clear responsibility; Applications do not nest within the same path.
- Namespaces are isolated: SecretStores, ExternalSecrets, and ServiceAccounts are namespace-scoped and non-shared.
- Everything is replayable from Git without manual steps; any required bootstrap is captured in `gitops/root`.

## Environment Parity

- Local kubeadm mirrors EKS behaviors: namespaces, controllers, GitOps flows, and secret delivery are identical.
- Expected differences: storage classes, TLS termination endpoints, and IAM integration. No other component behavior changes across environments.

## Failure Expectations

- Any cluster can be recreated from Git plus documented bootstrap (Argo CD install, Vault unseal/restore).
- Secret rotation is non-disruptive: Vault path rotation triggers ESO reconciliation; workloads observe updated Kubernetes Secrets.
- Argo CD outages do not block workloads; reconciliation resumes automatically when control plane recovers.
