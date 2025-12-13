# ADR 0001: GitOps as the Sole Control Plane

## Status
Accepted

## Context
The platform must be reproducible, auditable, and environment-agnostic. Manual changes introduce drift and erode trust boundaries.

## Decision
- Argo CD is the only reconciler. All platform and workload resources are applied through Argo CD Applications defined in Git.
- No imperative `kubectl` against platform namespaces. Drift is corrected automatically.
- Root bootstrap is limited to installing Argo CD itself and applying `gitops/root/root-app.yaml`.

## Consequences
- Every change is traceable to Git history and reviewed.
- Disaster recovery requires only Git plus documented Argo CD bootstrap.
- Operators must express all actions declaratively; ad-hoc fixes are disallowed.
