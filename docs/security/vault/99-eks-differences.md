# Differences Between Local kubeadm and EKS

Concepts stay identical (GitOps, Kubernetes auth, Vault policies). Infrastructure wiring changes.

| Area | Local kubeadm | EKS |
|----|----|----|
| Auth to Vault | ServiceAccount JWT | IRSA projects IAM role into Pod; Vault role bound to IAM principal |
| Vault Storage | In-memory dev or hostPath for demos | Highly available storage (S3 + DynamoDB or Raft on EBS) with auto-unseal (KMS) |
| Ingress | NodePort/host networking | AWS Load Balancer (NLB/ALB) with TLS termination and WAF where needed |
| TLS | Self-signed acceptable for homelab | Publicly trusted or private CA, enforced mTLS between components where supported |
| HA | Optional single-node | Multi-AZ control plane and Vault servers behind a stable address |
| Service Accounts | Standard JWTs | IRSA replaces static JWT validation with signed STS tokens; Kubernetes auth `aud` configured accordingly |
| Secrets Delivery | Same ESO contract | ESO service account annotated with IAM role; Vault policy maps to namespace/workload role as before |

Key migration tasks when moving to EKS:
- Replace Vault dev mode with HA cluster and KMS auto-unseal.
- Configure IRSA and update Vault Kubernetes auth to trust EKS OIDC issuer.
- Use AWS load balancers for Vault UI/API and Argo CD; enforce TLS everywhere.
- Move audit and metrics sinks to durable, centralized stores (CloudWatch/S3/managed Prometheus).
