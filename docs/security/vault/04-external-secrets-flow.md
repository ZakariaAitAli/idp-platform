# External Secrets Flow

## Control Loop (Detailed)
1. ESO controller starts and authenticates to Vault using a dedicated ServiceAccount and Vault role bound to that namespace.
2. ESO reads only the Vault paths allowed by the role’s policy (read/list). No write/delete/update capabilities are granted.
3. ESO materializes data into Kubernetes Secrets owned by the `ExternalSecret` resource. Secrets are recreated on drift or rotation.
4. Pods consume only these Kubernetes Secrets (via `envFrom`, `secretKeyRef`, or volume). Pods never receive Vault tokens or JWTs.
5. If Vault is unreachable, Kubernetes Secrets remain as last-synced data; ESO will reconcile once Vault is healthy again.

## SecretStore vs ExternalSecret
- **SecretStore / ClusterSecretStore**: Defines how the namespace authenticates to Vault (address, mount, role, CA, SA). One per namespace to avoid shared blast radius.
- **ExternalSecret**: Declares which Vault paths/keys to sync into which Kubernetes Secret. Workload owners write these manifests; platform owners own the SecretStore.

## Why Applications Never Access Vault Directly
- Eliminates secret sprawl and embedded credentials.
- Allows rotation without redeploying workloads (ESO rewrites the Secret).
- Keeps Vault protected by network and policy boundaries; only ESO holds a token.
- Simplifies audit: Vault audit log shows ESO reads; Kubernetes audit shows Pod mounts.

## Security Model
- ESO token TTLs are short and tied to the Kubernetes ServiceAccount JWT.
- Vault policies are namespaced and least-privilege (read/list only for exact paths).
- Kubernetes Secrets are namespaced derivatives; deleting them never deletes the source in Vault.
