# Secrets and Secret Delivery

## Sources and Storage
- Vault is the single source of truth for all secrets.
- No secrets are stored in Git, ConfigMaps, or container images.
- Secrets are namespaced in Vault by responsibility (e.g., `platform/<component>/...`, `workloads/<app>/...`).
- Engine choice is explicit (KV v2 for static secrets; transit for crypto if added later).

## Delivery Path
1. Secrets authored and rotated in Vault at well-defined paths.
2. External Secrets Operator authenticates to Vault via Kubernetes auth using the `external-secrets` ServiceAccount.
3. ESO reads only approved paths and writes Kubernetes Secrets into target namespaces.
4. Workloads consume Kubernetes Secrets; they never possess Vault tokens or credentials.

## Policy and Boundaries
- Vault policies are owned by the platform team; workload teams cannot create or edit policies or SecretStores.
- SecretStores/ClusterSecretStores are platform-scoped; ExternalSecrets are namespace-scoped and owned by workload teams.
- Each namespace gets its own ESO auth role and Vault path prefix to prevent cross-namespace access.

## Rotation
- Rotation starts in Vault; ESO reconciliation updates Kubernetes Secrets.
- Workloads must tolerate secret updates (env var reload or restart strategies) and avoid caching credentials indefinitely.
- Lease/TTL values are set in Vault; short-lived credentials are preferred over static values.
- Unseal/restore of Vault must precede platform reconciliation; ESO will fail closed if Vault is unavailable.

## Auditing
- Vault audit logging is mandatory; every secret read is recorded.
- Argo CD records all desired-state changes; ESO changes are observable via Kubernetes events and metrics.
- Regular review of Vault audit logs is required; unexpected paths or roles trigger incident response.
