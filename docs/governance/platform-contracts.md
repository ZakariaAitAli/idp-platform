# Platform Contracts

These contracts describe the boundaries between the platform team and workload teams. They are enforced through GitOps, namespaces, and policy.

## Ownership

- Platform team owns platform namespaces, CRDs, controllers, and Argo CD Projects/Applications.
- Workload teams own only their namespaces and resources created from approved templates.
- No shared mutable components across workload namespaces.

## Deployment

- All deployments flow through Argo CD Applications defined in `gitops/`.
- Workload onboarding uses platform-provided templates in `templates/`; manual manifests are rejected.
- Platform components live in `kubernetes/` and are reconciled by Argo CD Applications in `gitops/platform/`.

## Secrets

- Vault is the source of truth. No secrets in Git.
- External Secrets Operator is the only mechanism to write Kubernetes Secrets.
- Workload teams author `ExternalSecret` resources within their namespace against pre-provisioned `SecretStore` references; they cannot create or edit `SecretStore` or Vault policies.

## Identity and Access

- Human identity and SSO are handled by Keycloak; cluster access is federated through it.
- Workloads use Kubernetes ServiceAccounts mapped to Vault auth roles; no static credentials or Vault tokens in workloads.
- Namespace RBAC enforces least privilege. Cross-namespace access is disallowed unless explicitly granted by platform policy.

## Observability and Operations

- Metrics, logs, and dashboards are provided as platform services (Prometheus, Loki, Grafana). Workloads emit to platform sinks; they do not manage their own stacks.
- All platform changes are auditable in Git and Argo CD. Vault audit logs capture secret reads.

## Environment Parity

- Local (kubeadm) and AWS EKS share identical manifests and flows.
- Only storage classes, TLS endpoints, and IAM bindings differ per environment; overlays capture these differences.
