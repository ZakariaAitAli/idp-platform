# Vault Integration Model

## Auth Method
- Use Kubernetes auth (`kubernetes/` mount) as the single authentication mechanism for in-cluster consumers.
- Enable only JWT validation against the cluster issuer (or EKS OIDC provider when migrated).
- Tokens are short-lived, renewable, and non-root; disable child token creation.

## Roles and Binding
- Roles are namespaced: `<namespace>-eso` or `<namespace>-<workload>` when stricter isolation is required.
- Each role binds to a specific ServiceAccount and namespace and references exactly one policy.
- TTLs: minutes, with `max_ttl` bounded to prevent long-lived credentials.

## Policy Model
- Per-namespace read-only policies that map 1:1 to roles.
- Paths follow `kv/data/<env>/<namespace>/<workload>/*` to keep separation explicit.
- No wildcard roles spanning multiple environments.

## External Secrets Operator (ESO)
- ESO controller authenticates to Vault using its namespace ServiceAccount and the mapped role.
- The `SecretStore` references the Vault address, Kubernetes auth mount, role name, and ServiceAccount.
- ESO receives only read/list permissions for the approved paths and writes Kubernetes Secrets owned by the `ExternalSecret`.

## Migration to EKS
- Replace ServiceAccount JWT validation with IRSA; Vault role binds to the IAM role ARN projected into the ESO Pod.
- Update `aud` and OIDC discovery URL in the Vault Kubernetes auth config to match the EKS cluster issuer.
- Use KMS for Vault auto-unseal and durable storage backends (S3 + DynamoDB or Raft on EBS).
- Rotate all tokens and policies during cutover; do not reuse homelab JWT-based roles in production.
