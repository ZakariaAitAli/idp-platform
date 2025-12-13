# Vault Policies

## Policy Model
Vault policies are path-based and deny-by-default.
Access is granted explicitly per capability.

Capabilities include:
- read
- list
- create
- update
- delete

## Example Read-Only Policy

```
path "kv/data/my-app/*" {
capabilities = ["read"]
}
```


## Environment Separation
Policies must be environment-scoped.
Secrets for different environments never share paths.

Examples:
- kv/data/dev/my-app/*
- kv/data/staging/my-app/*
- kv/data/prod/my-app/*
