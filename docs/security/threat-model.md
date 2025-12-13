# Threat Model (High Level)

## Assets

- Secrets stored in Vault (root of trust)
- Cluster credentials and Argo CD admin access
- Workload namespaces and service identities
- Git repositories defining desired state

## Trust Boundaries

- Platform control plane namespaces (argocd, vault, eso, observability)
- Workload namespaces (isolated per team)
- Vault access via Kubernetes auth (ESO only)
- Git to Argo CD to cluster reconciliation path

## Assumptions

- Git is trusted and access-controlled; signed commits are recommended.
- Cluster nodes and etcd are hardened and patched.
- Vault is unsealed and backed by secure storage with audit logging enabled.
- Network policies restrict east-west traffic; control-plane endpoints are authenticated and encrypted.

## Primary Threats and Mitigations

- Secret exfiltration from Git: mitigated by never committing secrets; Vault-only.
- Unauthorized secret access: mitigated by Vault policies scoped per namespace, ESO read-only, audit logs on every read.
- Privilege escalation across namespaces: mitigated by RBAC boundaries, ServiceAccount scoping, and network policies.
- Bypass of GitOps: mitigated by Argo CD drift detection and enforcement; manual `kubectl` writes are overwritten.
- Supply chain tampering: mitigated by pinning container image sources, verifying manifests, and restricting Argo CD repository allowlists.
- Identity impersonation: mitigated by Keycloak-backed SSO, short-lived tokens, and workload identity mapping via ServiceAccounts.

## Residual Risks

- Compromise of Argo CD admin credentials can alter desired state; mitigate with SSO, MFA, and scoped Projects.
- Vault operator access is powerful; require strong auth, HSM-backed keys where possible, and strict audit review.
- Misconfigured overlays between kubeadm and EKS could create drift; enforce review and automated validation.
