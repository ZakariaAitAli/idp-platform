# Personal IDP Platform

A self-hosted Internal Developer Platform designed to model production-grade Kubernetes platform patterns using only open-source components.

This repository is not an application stack.
It is a platform reference implementation focused on security, GitOps, and operational correctness.

---

## Goals

- Reproduce real-world platform architecture in a homelab
- Enforce GitOps as the only deployment mechanism
- Centralize identity, secrets, and access control
- Prepare a direct migration path to managed Kubernetes (EKS)
- Treat platform components as first-class infrastructure

---

## Platform Principles

- No secrets in Git
- No imperative kubectl for platform components
- No direct application access to secret backends
- Clear separation between platform and workloads
- Environment-agnostic manifests
- Everything deployable via Argo CD

---

## Core Components

### Kubernetes
- Local cluster using **kubeadm**
- Designed to mirror managed Kubernetes behavior

### GitOps
- **Argo CD** manages all platform and workload deployments
- One-way sync from Git to cluster
- Platform components deployed as Argo CD Applications

### Secrets Management
- **Vault** as the single source of truth for secrets
- **External Secrets Operator** for Kubernetes-native secret delivery
- Applications consume Kubernetes Secrets only
- No application-to-Vault direct communication

### Identity and Access
- **Keycloak** for identity and authentication
- Platform-first RBAC model
- Environment and workload isolation by design

### Developer Experience
- **Backstage** as the internal developer portal
- Platform ownership clearly separated from application ownership

### Observability
- **Prometheus** for metrics
- **Loki** for logs
- **Grafana** for visualization
- Platform-level visibility, not app-specific hacks

---

## Repository Structure

- `docs/`  
  Platform documentation and architectural decisions

- `gitops/`  
  Argo CD root applications and environment overlays

- `kubernetes/`  
  Declarative manifests for platform components

- `templates/`  
  Enforced workload patterns and contracts

---

## Environments

- Local: kubeadm-based homelab
- Target: AWS EKS

All patterns implemented locally are designed to transfer to EKS with minimal changes (storage, TLS, IAM only).

---

## Non-Goals

- Application business logic
- CI pipelines
- Ad-hoc scripts
- One-off manual operations

---

## Status

Active development.
This repository evolves as platform capabilities are added and validated.

The goal is correctness and clarity, not speed.
