# Environments and Parity

## Environments
- Local: kubeadm-based homelab validating the full platform stack end-to-end.
- Target: AWS EKS with minimal, explicit drift.

## Expected Differences (Only These)
- Storage: Local PVs vs. EKS storage classes and optional S3/object storage for stateful components.
- TLS: Self-signed ClusterIssuer locally; Let’s Encrypt DNS-01 (Route53) on EKS (requires hosted zone + IAM role).
- Networking/IAM: MetalLB LoadBalancer locally; AWS NLB/ALB with annotations and IRSA on EKS.

## Invariants (Never Change)
- GitOps flow (Argo CD), secret delivery (Vault + ESO), namespace layout, RBAC, and templates.
- Vault paths and policies; only the auth backend configuration may vary per cluster.
- Platform components and versions; overlays only patch env-specific wiring.

## Overlays and Apps
- `gitops/environments/local` vs `gitops/environments/eks`: App-of-apps that enable the appropriate overlays.
- Component overlays under `kubernetes/*/overlays/{local,eks}`: ingress service type/annotations, cert-manager solvers, MetalLB presence, storage classes.

## Bootstrap Expectations Per Environment
- Local: Provide MetalLB IP pool, optional storage class defaults, and accept self-signed TLS for initial access.
- EKS: Pre-create Route53 hosted zone, IAM role for cert-manager DNS-01, IAM roles for service accounts (IRSA) for controllers needing AWS APIs, and storage classes.
