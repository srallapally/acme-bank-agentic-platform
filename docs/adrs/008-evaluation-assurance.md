# ADR-008 — Evaluation and release assurance

**Status:** Proposed · **Phase:** P0/P5 · **Owners:** AI Assurance + Risk (assign) · **Open:** [OI-010](../open-issues.md#oi-010)

## Context
A high task-success score can mask authorization bypass, PII leakage, or wrong banking state. LLM judges are not trustworthy financial authorization oracles.

## Decision
Maintain versioned golden, negative/security, adversarial and recovery fixtures. Use AgentCore Evaluations for quality/trajectory, pytest for deterministic banking/Cedar invariants, and OSS DeepEval/promptfoo as appropriate. Critical cross-customer exposure, unauthorized writes and leaked restricted PII **block release**, independent of average quality score. Keep synthetic-only eval data in MVP.

## Alternatives
LLM-as-judge alone (rejected), manual review only (insufficient regression evidence), online production-only testing (unacceptable).

## Consequences
Scoring versions, datasets and model/prompt/tool/policy versions must be preserved. Define threshold owners, sample counts and documented gaps; passing a test suite does not prove safety.

## Verification
CI executes a cross-customer deny scenario, malicious tool response injection, PII canary trace test and synthetic payment idempotency test; archived report links to exact artifact versions.

## Open
[OI-010](../open-issues.md#oi-010).
