# ADR-006: PII minimization at inference, tool and telemetry boundaries

**Status:** Proposed · **Phase:** P0/P5 · **Owner/approvers:** TBD

## Context
Sensitive bank data can escape through model prompts, tool results, evaluation sets, traces and logs. Probabilistic PII detection alone is insufficient.

## Decision
Security/Data Protection owns classification rules. MCP APIs enforce field allowlists, opaque identifiers and last-four masking where practical; OSS Presidio detects PII in unstructured content; middleware minimizes before inference; output and telemetry exporters sanitize before disclosure/storage. De-tokenization stays in authorized deterministic backend services. Use synthetic MVP data only.

## Alternatives
Redaction only at final response (rejected); guardrails-only PII policy (rejected); storing raw transcript in default telemetry (rejected).

## Consequences
False positives/negatives need fixture coverage and approved exceptions; authorized customer data must not be indiscriminately suppressed. Decide retention, region/model routing and audit access before real data.

## Verification
Canary PII fixtures across prompts, MCP responses, telemetry and eval export; check authorized disclosure and denied cross-customer data exposure.

**Open:** [OI-011](../open-issues.md), [OI-012](../open-issues.md).
