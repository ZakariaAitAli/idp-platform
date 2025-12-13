# External Secrets Flow

## Control Loop
1. External Secrets Operator (ESO) authenticates to Vault.
2. ESO reads secrets according to its Vault policy.
3. ESO writes Kubernetes Secrets.
4. Pods consume Kubernetes Secrets only.

## SecretStore vs ExternalSecret
- SecretStore defines how to access Vault.
- ExternalSecret defines what data to sync.

## Security Model
ESO has limited Vault permissions.
Applications have no Vault permissions.
Vault remains the single source of truth.
