# ADR-004 — Cedar at tool and resource boundaries

**Status:** Proposed · **Phase:** P0/P2 · **Owners:** Authorization + Banking (assign) · **Open:** [OI-005](../open-issues.md#oi-005), [OI-006](../open-issues.md#oi-006)

## Context
A tool permission does not establish account ownership or financial authorization. AgentCore managed policy and upstream OSS Cedar may have different schemas and integration contracts.

## Decision
Use AgentCore Gateway Cedar policies to constrain *which MCP tool may be invoked*. Run an upstream OSS Cedar PDP behind SPIFFE mTLS in EKS for *which bank account/payment resource* the verified principal may access. Independent backend checks are required even after Gateway permit; policies default deny and explicit forbid takes precedence. Build context from authenticated token/workload evidence and authoritative banking records, not LLM input.

## Alternatives
Gateway policy only (insufficient resource visibility); single custom authorizer for everything (loses managed Gateway control); authorization in prompts (rejected).

## Consequences
Two deployment schemas, distinct policy test suites, versioned policy releases, policy outage fail-closed for sensitive writes; prevent TOCTOU by reauthorizing at commit.

## Verification
Schema-validate both policy sets; test allowed account and cross-customer denial, missing claims, forged SPIFFE string, policy outage, deny override, and denied direct API bypass.

## Open
[OI-005](../open-issues.md#oi-005), [OI-006](../open-issues.md#oi-006).
