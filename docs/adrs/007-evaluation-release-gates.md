# ADR-007: Deterministic and model-based evaluations as release gates

**Status:** Proposed · **Phase:** P0/P5/P6 · **Owner/approvers:** TBD

## Context
A good natural-language answer or high completion score does not show that an agent respected entitlements or preserved financial integrity.

## Decision
Use pytest and integration fixtures for hard invariants (Ping, SPIRE, Cedar, PII, idempotency, authorized state). Use AgentCore Evaluations and OSS DeepEval/promptfoo for task quality, tool trajectories and prompt-injection stress. Version datasets, prompt/model, image, policy and tool contracts. Block release on known critical authorization bypass, forbidden data disclosure or unauthorized financial-state mutation. Synthetic environment only.

## Alternatives
LLM-as-judge only (rejected); manual demo-only signoff (rejected).

## Consequences
Set quantifiable task-quality SLOs and budgets with risk owners; publish scenario counts, limitations and unresolved risks. A finite all-green suite does not prove absence of vulnerabilities.

## Verification
CI runs golden, negative, adversarial and fault cases; retained release report can reproduce failures and correlate sanitized traces. Emergency revocation and recovery drills are mandatory for MVP acceptance.

**Open:** [OI-013](../open-issues.md), [OI-014](../open-issues.md).
