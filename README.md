# Internal Developer Platform Reference

This repository is a self-hosted Internal Developer Platform (IDP) reference implementation. It is not an application stack, not a CI pipeline, and not a collection of ad-hoc scripts. Everything here exists to model a production-grade Kubernetes platform using only open-source components, operated exclusively through GitOps, and portable from local kubeadm to AWS EKS.

---

## Scope and Intent

- Deliver a complete platform baseline for multi-tenant Kubernetes clusters
- Enforce GitOps as the single control plane for all platform and workload changes
- Centralize identity, secrets, and policy with clear platform/workload boundaries
- Provide a direct path from local kubeadm to AWS EKS with minimal drift (storage, TLS, IAM are the expected variances)
- Treat platform components as first-class, lifecycle-managed infrastructure

---

## Platform Principles

- No secrets in Git. Vault is the single source of truth.
- External Secrets Operator is the only secret delivery mechanism to Kubernetes.
- Applications never talk to Vault directly; they consume Kubernetes Secrets only.
- Argo CD is the only deployment authority; no imperative `kubectl` for platform components.
- Clear ownership boundaries: platform team owns platform namespaces, CRDs, and controllers; workload teams own their namespaces and use platform-approved templates.
- Manifests are environment-agnostic; overlays handle environment-specific configuration.
- Everything is declarative, observable, and recoverable from Git.

---

## Core Components (Production-Intent)

- Kubernetes: Local kubeadm cluster mirroring managed behaviors; target is AWS EKS.
- GitOps: Argo CD with app-of-apps pattern for platform and workload onboarding.
- Secrets: HashiCorp Vault as source of truth; External Secrets Operator for syncing to Kubernetes Secrets.
- Identity and Access: Keycloak for identity and authentication; RBAC enforced at platform and namespace boundaries.
- Developer Portal: Backstage for catalog, ownership, and golden paths.
- Observability: Prometheus (metrics), Loki (logs), Grafana (visualization) for platform-level visibility.

---

## Environments

- Local: kubeadm-based homelab used for validation of the full platform stack.
- Target: AWS EKS with expected adjustments limited to storage classes, TLS termination, and IAM integration. All other behaviors and contracts remain invariant.

---

## Repository Layout

- `docs/` — Architecture, ADRs, threat model, platform contracts, and operational expectations.
- `gitops/` — Argo CD root applications, app-of-apps definitions, and environment overlays (local, EKS).
- `kubernetes/` — Declarative manifests for platform components only; no application workloads.
- `templates/` — Enforced workload blueprints and golden paths consumed by workload teams.

No CI pipelines, no application code, no helper scripts, and no manual runbooks are maintained here; all operations flow through GitOps.

---

## Operating Model

- Git is the source of truth; Argo CD reconciles desired state to clusters.
- Platform components are deployed as Argo CD Applications; workloads onboard through platform templates and overlays.
- Secrets are authored in Vault and projected via External Secrets; Kubernetes Secrets are the only consumable interface for workloads.
- Identity, access, and namespace boundaries are explicit; platform and workload responsibilities do not overlap.

---

## Non-Goals

- Application business logic or service code
- CI/CD pipelines or runners
- Manual operations or imperative scripts
- Ad-hoc tooling that bypasses GitOps
