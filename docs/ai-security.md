# OSS AI Security and MCP Controls

## Approach
Use OSS **NeMo Guardrails** and **LLM Guard** for input/output and conversational safeguards; OSS **Presidio** for PII detection. None of these authorizes financial operations. Gateway Cedar and backend Cedar remain authoritative; backend validates all schemas/arguments.

## Controls
- Treat user text, documents, retrieval results, tool descriptions, MCP responses and peer-agent messages as untrusted.
- Pin tool manifests and JSON Schemas; approve new tools through review; prevent arbitrary dynamic MCP target URLs.
- Read-only tools distinct from side-effect tools; prepare/approve/commit state machine for post-MVP funds movement.
- Allowlist service egress; restrict SSRF and metadata endpoints; limit tool depth, tokens, requests and retry counts.
- Guardrails before model inference, at tool boundary where supported and before sensitive output/telemetry export.
- Never let an LLM approve its own transaction, grant scopes, modify policy or resolve arbitrary sensitive tokens.
- Correlate every tool call with task, user, delegated actor, tool/schema version, Cedar policy decision and sanitized audit ID.

## Evaluation scenarios
Direct and indirect prompt injection, poisoned tool descriptions, tool result spoofing, agent authority confusion, cross-tenant leakage, sensitive output laundering, malicious JSON arguments and unauthorized tool chaining.

## Failure behavior
Critical authorization and data leakage checks fail closed; guardrail classifier output is probabilistic and may err. Limit false positives with human-reviewed fixtures while never waiving deterministic account/payment invariants.
