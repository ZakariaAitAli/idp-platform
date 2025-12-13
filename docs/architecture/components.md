# Platform Components

Catalog of platform services with purpose, ownership, and operational expectations. All components are declared as Argo CD Applications under `gitops/platform` with manifests in `kubernetes/<component>`.

## Argo CD (GitOps Control Plane)
- Purpose: Only reconciler for platform/workload resources; enforces desired state from Git.
- Ownership: Platform team.
- Key manifests: `kubernetes/argocd/*` (ArgoCD CR + namespace).
- Operations: Automated sync with prune/self-heal; no imperative `kubectl` in platform namespaces; repository allowlist is this repo only by default.
- Integrations: Expected to use Keycloak for SSO and project-based RBAC.

## Vault + External Secrets Operator
- Purpose: Vault is source of secret truth; ESO materializes namespace-scoped Kubernetes Secrets.
- Ownership: Platform team owns Vault install, policies, auth roles, and (Cluster)SecretStores; workload teams own `ExternalSecret` objects only.
- Key manifests: `kubernetes/vault/*`, `kubernetes/external-secrets/*`.
- Operations: Secret rotation originates in Vault; ESO reconciliation updates Kubernetes Secrets. No secrets in Git. No application-to-Vault traffic.
- Environment drift: Vault storage classes and listeners vary per env; ESO remains the same.

## Ingress NGINX
- Purpose: North-south traffic ingress with per-environment LB configuration.
- Ownership: Platform team.
- Key manifests: `kubernetes/ingress-nginx/base` plus overlays per env.
- Operations: All ingress points defined in Git; service type/annotations patched per env (MetalLB IP locally, NLB annotations on EKS).

## Cert-Manager
- Purpose: Certificate issuance for platform and ingress endpoints.
- Ownership: Platform team.
- Key manifests: `kubernetes/cert-manager/base` and overlays per env.
- Operations: Local uses self-signed ClusterIssuer; EKS uses Let’s Encrypt DNS-01 via Route53 (IAM role required). Solver differences are captured in overlays.

## Keycloak
- Purpose: Identity provider for humans/services; backs SSO for Argo CD and cluster access.
- Ownership: Platform team.
- Key manifests: `kubernetes/keycloak/*` (Deployment, ServiceAccount, ExternalSecret, Service).
- Operations: Database credentials delivered via ExternalSecret; SSO configuration expected in Vault-managed config. Enforce MFA where supported.

## Backstage
- Purpose: Developer portal for catalog, ownership, and golden paths.
- Ownership: Platform team for infra; service owners manage catalog data.
- Key manifests: `kubernetes/backstage/*`.
- Operations: App config delivered via ExternalSecret; runs behind ingress; never bypasses platform auth.

## Observability Stack (Prometheus, Loki, Grafana)
- Purpose: Shared metrics, logs, dashboards for platform and workloads.
- Ownership: Platform team.
- Key manifests: `kubernetes/observability/*` (Grafana anchor plus datasources; Prometheus/Loki assumed provisioned similarly).
- Operations: Workloads emit to platform sinks; admin credentials via ExternalSecret; data sources defined in ConfigMap. Extend with storage backends per env.

## MetalLB (Local Only)
- Purpose: Provide LoadBalancer semantics on kubeadm clusters.
- Ownership: Platform team.
- Key manifests: `kubernetes/metallb/local/*`.
- Operations: IP pools and advertisements are Git-defined; absent on EKS.
