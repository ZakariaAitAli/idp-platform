# Secret Engines

## KV Version 2
The default secret engine for static secrets.
Provides versioning, soft deletes, and metadata.

Use cases:
- API keys
- OAuth tokens
- Static credentials

## Dynamic Secrets
Optional engines generate credentials on demand.
Examples:
- Database credentials
- Cloud provider credentials

## Engine Selection Principles
- Prefer kv-v2 as baseline
- Use dynamic engines only when rotation is mandatory
- Never expose engine details to applications
