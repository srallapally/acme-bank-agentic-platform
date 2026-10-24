# Threat Model — MVP

## Assets and trust assumptions
**Assets:** customer tokens, account data, immutable payment intents, SPIRE signing keys, Cedar policies, MCP tool definitions, model context, telemetry, and release evidence. **Actors:** legitimate customers/employees; compromised browser/client; attacker-controlled documents/prompts; malicious MCP response; compromised EKS pod; overly privileged agent; misconfigured administrator. Assume prompts and tool output are hostile; managed cloud boundaries are not automatically part of the bank SPIFFE trust domain.

## Attack surfaces and controls
| Threat / abuse case | Boundary | Mitigation | Proof |
| --- | --- | --- | --- |
| Cross-customer access | Ping → MCP → bank | Verified subject/resource, backend Cedar, no user-supplied account ownership | Deny fixtures |
| OAuth token replay/audience abuse | Client/controller/Gateway | Issuer, audience, expiry, actor, scope validation; narrow TTL; no refresh tokens in agent | Replay/negative tests |
| Forged SPIFFE identity | EKS workload calls | SPIRE attest, verify X.509 chain + exact peer ID | Wrong-SVID test |
| Prompt injection in retrieved data | Tool result → agent | Treat as data; allowlisted tools; deterministic tool authorization | Indirect injection suite |
| Tool-description poisoning | MCP registration | Reviewed registry, schema hash and approved version | Tampered manifest rejection |
| Gateway bypass | Agent → backend | Private networking and independent backend auth | Direct-access denial |
| PII in trace/eval | App → telemetry | Field minimization and sanitize-before-export | Canary PII fixture |
| Rogue tool requests/SSRF | MCP/network | Strict tool schemas and outbound destinations, block metadata endpoint | Egress checks |
| Duplicate payment on retries | Agent → banking | Immutable intent, idempotency and atomic conditional writes | Concurrent replay tests |
| Policy tampering/stale grants | CI/PDP | Policy review/version pin, safe rollout, commit-time auth and revocation | Deny after revoke |
| Certificate/root compromise | SPIRE | Protect trust anchor, short SVID TTL, allowlist, incident isolation | Rotation/restore drills |
| Model-driven tool loops/cost | Runtime/Gateway | Tool limits, budgets, timeouts and kill switch | Budget-bound tests |

## STRIDE checklist
- **Spoofing:** Ping/JWT, SPIFFE chain/peer validation, ingress identity propagation.
- **Tampering:** signed artifacts, immutable payment intents, policy change controls.
- **Repudiation:** correlated sanitized audit records, policy/model/tool versions.
- **Information disclosure:** Presidio, field-level restrictions, encryption, egress isolation.
- **Denial of service:** bounded budgets, concurrency, rate limits, fail-closed critical calls.
- **Elevation of privilege:** least-privilege delegation, independent Cedar at every business effect.

## Open risk decisions
Appoint risk owners and define control effectiveness, evidence retention, response playbooks, cancellation semantics and allowable residual risks before production. See [penetration testing](penetration-testing.md).
