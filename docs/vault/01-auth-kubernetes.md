# Kubernetes Authentication Flow

## Objective
Allow workloads running in Kubernetes to obtain scoped access to secrets without embedding credentials.

## Authentication Flow
1. A Pod runs with a Kubernetes ServiceAccount.
2. Kubernetes projects a signed JWT into the Pod.
3. Vault validates the JWT against the Kubernetes API server.
4. Vault maps the identity to a Vault role.
5. Vault issues a short-lived token bound to policies.

## Required Kubernetes Objects
- ServiceAccount used for identity
- ClusterRoleBinding granting `system:auth-delegator`

## Required Vault Objects
- Kubernetes auth method
- Vault role binding ServiceAccount and namespace
- One or more policies

Authentication establishes identity only.
Authorization is enforced exclusively by policies.
