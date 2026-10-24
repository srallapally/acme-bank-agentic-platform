# ADR-008 — Python/TypeScript monorepo

**Status:** Proposed · **Phase:** P0 · **Owners:** Engineering Leads (assign) · **Open:** [OI-009](../open-issues.md)

## Context
ACME needs fast iteration without language duplication or a complex deployment graph.

## Decision
Single monorepo. Python 3.12+ / Strands for agent, FastMCP/Pydantic for MCP, FastAPI for backend. TypeScript/React/Vite for demonstration UI. Terraform/Helm/GitHub Actions for infrastructure and CI. JSON Schema and OpenAPI as shared contracts. Prefer small upstream Rust Cedar PDP rather than rewriting the policy evaluator. Tooling: uv, Ruff, mypy, pytest, Vitest.

## Alternatives
All-TypeScript backend, all-Python UI, multiple repos, service mesh at day one. Defer those unless requirements demonstrate value.

## Consequences
Cross-language DTO generation required and release paths need scoped CI. Independent deployment artifacts despite a shared repository.

## Verification
Generate TS API client from canonical schema; lint/typecheck/test affected packages; build each deployable service in isolation; check no secrets or production data in fixtures.

## Open
[OI-009](../open-issues.md#oi-009).
