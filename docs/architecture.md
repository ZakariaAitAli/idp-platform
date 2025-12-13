# Platform Architecture Overview

## Control Plane Composition
- **GitOps**: Argo CD is the only reconciler. All platform components are described as Argo CD Applications; no imperative `kubectl apply`.
- **Security**: Vault is the root of secret truth. External Secrets Operator (ESO) is the only bridge into Kubernetes Secrets.
- **Identity**: Keycloak issues identities for humans and services; Kubernetes ServiceAccounts represent workload identities.
- **Developer Experience**: Backstage is the single entrypoint for discoverability; it never bypasses platform controls.
- **Observability**: Prometheus, Loki, and Grafana operate as platform services, not embedded per-app add-ons.

## End-to-End Flow
1. Git holds declarative state (Argo CD Applications, raw manifests, Helm values).
2. Argo CD reconciles Git state into the cluster namespaces dedicated to each component.
3. Vault stores and governs secrets; it never faces workloads directly.
4. ESO authenticates to Vault via Kubernetes auth, pulls scoped secrets, and writes Kubernetes Secrets.
5. Workloads consume only Kubernetes Secrets and never embed Vault tokens or credentials.

## Trust Boundaries
- **Platform Administrators**: Full control of Argo CD Projects/Applications and Vault policies. No direct modification inside workload namespaces except for shared platform services.
- **Application Teams**: Write ExternalSecret manifests in their namespace and reference pre-approved paths. They cannot write Vault policies or SecretStores.
- **Argo CD**: Cluster-admin for the namespaces it manages; cannot read Vault secrets (only applies manifests).
- **External Secrets Operator**: Scoped Vault policy allowing read-only access to approved paths. Does not push data anywhere else.
- **Workloads**: Access limited to Kubernetes Secrets in their namespace. No network path or credentials to Vault.
- **Auditors**: Read-only access to Git and Argo CD state; Vault audit logs retained for all secret reads.

## Operational Invariants (Must Never Be Violated)
- Git is the single source of desired state; no manual drift is tolerated.
- Vault is the only source of secret truth; Kubernetes Secrets are derivatives and may be recreated at any time.
- Applications never speak to Vault directly and never ship Vault tokens.
- One Argo CD Application per responsibility; no Application points at a path containing another Application manifest.
- Raw manifests live under `kubernetes/*`; Helm charts are installed only via Argo CD Applications.
- Namespaces are isolated: SecretStores, ExternalSecrets, and workload ServiceAccounts must not be shared across namespaces.
- Changes must be replayable on a wiped cluster without out-of-band steps.
