# Repository Structure (Authoritative)

- `docs/` — Platform reference documentation (architecture, governance, security, operations, environments, ADRs).
- `gitops/` — Argo CD Applications.
  - `root/` — Bootstrap (root app, projects, platform app-of-apps, per-environment app-of-apps).
  - `platform/` — Applications for platform components (argocd, cert-manager, ingress, vault, eso, keycloak, backstage, observability).
  - `environments/{local,eks}/` — Environment-specific Applications (e.g., ingress and cert-manager overlays, MetalLB for local).
- `kubernetes/` — Declarative manifests consumed by Argo CD Applications.
  - Component directories (argocd, cert-manager, ingress-nginx, external-secrets, vault, keycloak, backstage, observability, metallb).
  - Overlays under components where environment differences exist (e.g., `ingress-nginx/overlays/local` vs `.../eks`).
- `templates/` — Golden paths for workloads (currently a stateless service template with Namespace, SA, Deployment, Service, ExternalSecret, NetworkPolicy).

## Flow
1) Argo CD (installed once) applies `gitops/root/root-app.yaml`.
2) Root app applies projects, platform app-of-apps, and the selected environment app-of-apps.
3) Platform app-of-apps reconciles component Applications that source `kubernetes/<component>`.
4) Environment apps reconcile overlays (LB, TLS, IAM) on top of base components.
5) Workload teams copy templates into their repos and onboard via a workload AppProject (not included here) pointing at their repo path.
