# Differences Between Local kubeadm and EKS

| Area | Local kubeadm | EKS |
|----|----|----|
| Auth | ServiceAccount JWT | IRSA + ServiceAccount |
| Storage | In-memory | Integrated / DynamoDB / S3 |
| HA | Disabled | Enabled |
| TLS | Optional | Mandatory |
| IAM | N/A | AWS IAM enforced |

All authentication and policy concepts remain identical.
Only infrastructure concerns change.
