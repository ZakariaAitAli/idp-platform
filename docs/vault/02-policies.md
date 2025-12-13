# Vault Policies

## Policy Model
Vault policies are path-based and deny-by-default. Capabilities are granted explicitly (`read`, `list`, `create`, `update`, `delete`). Controllers should receive only `read` and `list`.

## Namespace-Scoped Policy Pattern
- One policy per namespace (e.g., `kv/data/dev/<team>/*`).
- Optional tighter scoping per workload when cross-team namespaces exist.
- Policies are never shared across namespaces or environments.

### Example Read-Only Policy
```
path "kv/data/dev/example-app/*" {
  capabilities = ["read", "list"]
}
```

## Environment Separation
Policies and secret paths are environment-scoped. Secrets for different environments never share paths and never reuse tokens.

Examples:
- `kv/data/dev/example-app/*`
- `kv/data/staging/example-app/*`
- `kv/data/prod/example-app/*`

## Ownership and Change Control
- Platform team owns policy creation and binding to roles.
- Application teams request paths; they never edit policies directly.
- Policy updates are reviewed like code changes and deployed via GitOps (Vault config is treated as IaC even if applied separately).
