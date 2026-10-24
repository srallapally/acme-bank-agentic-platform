# ADR-005: Domain MCP servers with constrained tool contracts

**Status:** Proposed · **Phase:** P0/P3/P4 · **Owner/approvers:** TBD

## Context
Dynamic tools and model-generated arguments can expand financial risk. The MVP only needs accounts read and payment preparation.

## Decision
Deploy separate Accounts and Payments Python/FastMCP services on EKS with pinned MCP/tool schemas and reviewed registration. Tool responses return only policy-allowed fields. Require strict Pydantic/JSON Schema validation, bounded timeouts and no arbitrary destinations. Prefer `accounts.get_balance`, `payments.get_beneficiary`, `payments.prepare`, `payments.get_status`. Do **not** expose real `commit_payment` in MVP. Persist immutable synthetic intent and idempotency key; no direct agent database access.

## Alternatives
One all-powerful MCP server (rejected); unrestricted payment transfer tool (rejected); runtime-discovered unreviewed third-party tools (rejected).

## Consequences
Tool descriptions are untrusted input and must be versioned/approved; Gateway may limit tool exposure but backends remain independently responsible for validation/authorization.

## Verification
Contract tests, malformed argument rejection, cross-customer denial, duplicate preparation/idempotency, malicious tool-description fixture, unapproved tool discovery and agent-to-backend bypass tests.

**Open:** [OI-009](../open-issues.md), [OI-010](../open-issues.md).
