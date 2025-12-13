# GitOps Model

Authoritative description of how desired state flows from Git to clusters.

## App-of-Apps Structure
- `gitops/root/root-app.yaml`: Minimal bootstrap applied once after Argo CD install to let Argo CD manage itself and everything else.
- `gitops/root/platform.yaml`: App-of-apps for platform components (argocd, cert-manager, ingress, vault, eso, keycloak, backstage, observability).
- `gitops/root/env-local.yaml` and `gitops/root/env-eks.yaml`: Environment overlays for infra differences (LB type, TLS issuers, IAM). Only one is synced per cluster.

## Projects and Boundaries
- `platform` project: Cluster-scoped resources allowed; used only by platform Applications.
- `workloads` project: Namespace-scoped by default; cluster resources limited to reviewed types (e.g., NetworkPolicy). Workload repos must align to templates.

## Sources of Truth
- Argo CD repository allowlist is this repo. Additional repos require governance approval.
- `kubernetes/`: Component manifests and Kustomize overlays per environment.
- `templates/`: Golden paths for workloads; deviations require approval.
- `docs/`: Operational and architectural reference; not applied by Argo CD but must stay aligned.

## Sync and Enforcement
- Automated sync with prune and self-heal on all platform apps; drift is an incident.
- No imperative `kubectl` in platform namespaces; changes go through PRs.
- One Argo CD Application per responsibility; no Applications pointing at paths containing other Application manifests.
- Namespace creation via Applications must be explicit; `CreateNamespace=true` is used only where intended.

## Bootstrap Expectations
- Install Argo CD once manually, then apply `gitops/root/root-app.yaml`.
- Replace placeholder repo URL in root manifests before first sync.
- Secrets and IAM bindings required for cert-manager (EKS) and Vault must exist or be provisioned via IaC prior to sync.
