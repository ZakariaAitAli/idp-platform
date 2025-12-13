# Workload Secret Consumption Rules

Applications do not authenticate to Vault.
Applications do not embed secrets.
Applications consume Kubernetes Secrets only.

Secrets are:
- Defined in Vault
- Synced by External Secrets Operator
- Delivered by Kubernetes

This contract is mandatory for all workloads.
