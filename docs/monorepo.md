# Monorepo Design

One repository, multiple independently deployable services. Keep runtime concerns separated; share **contracts**, not highly coupled runtime libraries.

```text
acme-bank-agentic-platform/
  README.md
  docs/                          # Architecture, testing, decisions
  apps/banking-web/              # TypeScript/React/Vite demo
  agents/banking-agent/          # Python AgentCore runtime
  services/
    session-controller/          # FastAPI; user/session binding
    accounts-mcp/                # FastMCP
    payments-mcp/                # FastMCP
    banking-simulator/           # Synthetic accounts and payment state
    authorization-pdp/           # Upstream Cedar evaluator adapter (Rust optional)
    pii-protection/              # Presidio-based service
  packages/
    python-common/               # Limited auth-neutral utilities only
    contracts/openapi/           # REST contracts, canonical
    contracts/jsonschema/        # MCP schemas and fixtures
  identity/ping/                 # Nonsecret config/examples
  identity/spire/                # Registrations/allowlists
  policies/cedar/                # Gateway and backend policy bundles
  policies/guardrails/           # OSS AI safeguards
  evaluations/                   # Dataset manifests, judges, invariants
  infrastructure/
    terraform/                   # Module + env separation
    helm/                        # EKS, SPIRE and Envoy
  tests/                         # Cross-service integration/security
  .github/workflows/
```

This is a **target layout**, not a claim that directories exist.

## Dependency rules
- Frontend calls controller APIs only; no direct model, Gateway or MCP credentials in browser.
- Agent calls approved MCP tools via Gateway; never imports backend database clients.
- MCP servers call banking APIs and Cedar PDP through authenticated interfaces; no model SDK dependency in business services.
- Shared packages contain schemas, trace/correlation helpers and harmless validation only. Identity decisions live in server-side enforcement points.
- Contracts are versioned; incompatible MCP schema changes require independent review.

## Development and CI
- Python: `uv sync`, `ruff check .`, `mypy` on relevant packages, `pytest` unit and integration suites.
- UI: pinned package manager, `tsc --noEmit`, lint, `vitest`, build.
- IaC: `terraform fmt -check`, `terraform validate`, security scan, `helm lint`; plan before apply.
- Security: Cedar validation/policy fixtures, bad-token tests, wrong SPIFFE ID, PII exclusion, prompt-injection cases.
- CI paths filters allow focused checks but changes to shared contracts/policies trigger all affected service tests.
- PRs must show action, verification, impact, rollback and any residual risks. Secrets and production customer data never enter test fixtures.
