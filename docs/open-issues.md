# Open Issues, Assumptions and Decision Register

**Scope:** ACME Bank agentic MVP · **Last reviewed:** 2026-10-24 · **Maintenance:** update in the same PR that adds evidence, decides an assumption or changes an ADR. This Markdown register tracks **architecture questions**, separate from implementation stories in [GitHub Project](https://github.com/users/srallapally/projects/5).

**Status meanings:** Open = unresolved; In validation = being tested; Resolved = evidence and decision documented; Deferred = explicitly excluded from MVP. **Priority:** P0 blocks relevant architecture/phase exit; P1 matters before MVP release; P2 post-MVP.

| ID | Priority | Status | Type | Open item / assumption to verify | Owner | Closure evidence / decision |
| --- | --- | --- | --- | --- | --- | --- |
| OI-001 | P0 | Open | Lifecycle | Validate AgentCore task/session isolation, TTL, reuse prevention and kill-switch behavior | Platform TBD | Session/revocation test, [ADR-001](adrs/001-ephemeral-agent-execution.md) |
| OI-002 | P0 | Open | Integration | Confirm private AgentCore Gateway → EKS MCP target configuration, TLS, outbound auth and routing | Platform TBD | Live integration PoC and captured config, [ADR-002](adrs/002-managed-to-spiffe-trust-boundary.md) |
| OI-003 | P0 | Open | Identity propagation | Verify Ping subject/actor/scopes cross Gateway ingress without spoofable headers or unauthorized token exchange | IAM TBD | End-to-end positive and negative auth traces, [ADR-002](adrs/002-managed-to-spiffe-trust-boundary.md), [ADR-003](adrs/003-ping-oauth-delegation.md) |
| OI-004 | P0 | Open | Transport | Agree managed-to-SPIFFE transition, cert termination points, peer allowlists and private CA strategy | Security TBD | Transport matrix + wrong-SVID tests, [ADR-002](adrs/002-managed-to-spiffe-trust-boundary.md) |
| OI-005 | P0 | Open | Ping | Confirm installed Ping OAuth token-exchange/actor semantics, JWT claims, scopes and short TTL | IAM TBD | Ping sandbox configuration and tests, [ADR-003](adrs/003-ping-oauth-delegation.md) |
| OI-006 | P0 | Open | Cedar | Generate/validate AgentCore tool-action Cedar schema; map to backend account/resource schema | Auth TBD | Checked-in schemas, tests, [ADR-004](adrs/004-cedar-enforcement.md) |
| OI-007 | P1 | Open | Policy operations | Set PDP outage behavior, policy rollout, freshness and revocation service-level objectives | Auth/SRE TBD | Failure/revocation tests, [ADR-004](adrs/004-cedar-enforcement.md) |
| OI-008 | P1 | Open | Durable state | Choose/verify synthetic payment intent persistence, idempotency and recovery semantics; Step Functions necessary? | Banking TBD | Crash/retry/concurrency evidence, [ADR-001](adrs/001-ephemeral-agent-execution.md) |
| OI-009 | P0 | Open | MCP contract | Finalize Accounts/Payments MCP tool schemas, immutable plan semantics, approvals and exposure allowlist | Agent/Banking TBD | Contract fixtures, [ADR-005](adrs/005-mcp-contracts.md) |
| OI-010 | P1 | Open | Tool supply chain | Define schema signing/hash, admission review and dynamic tool discovery restrictions | Security TBD | Tampered-tool regression, [ADR-005](adrs/005-mcp-contracts.md) |
| OI-011 | P1 | Open | PII | Define bank data classes, permitted model fields, deterministic mask/token rules and Presidio recognizers | Data Protection TBD | Approved class matrix + canary test, [ADR-006](adrs/006-data-protection.md) |
| OI-012 | P1 | Open | Data residency | Approve inference region/routing, logging destinations, retention, deletion and evidence access | Security/Privacy TBD | Privacy/data-flow review, [ADR-006](adrs/006-data-protection.md) |
| OI-013 | P1 | Open | Evals | Approve golden set, negative suite, release thresholds and deterministic blockers | QA/Risk TBD | Versioned fixtures and CI results, [ADR-007](adrs/007-evaluation-release-gates.md) |
| OI-014 | P1 | Open | Pentest | Set authorized ROE, isolated targets, penetration testing scope and signoff owners | Security TBD | Signed ROE and report, [ADR-007](adrs/007-evaluation-release-gates.md) |
| OI-015 | P1 | Open | Operations | Define egress restrictions, cost budgets, rollback, telemetry redaction and disaster recovery basics | SRE TBD | Operational drill/runbook, [MVP plan](mvp-delivery.md) |
| OI-016 | P2 | Deferred | Expansion | Multi-agent delegation, production payment posting, region federation and enterprise registry rollout | Product TBD | Separate post-MVP ADRs and signoff |

## Assumptions currently used in the design
- Only synthetic customer/account/beneficiary data; **no live payment execution**.
- Python 3.12+ agent/backend/MCP, TypeScript/React UI, Terraform and EKS; all are choices, not deployed facts.
- One ephemeral AgentCore session per task; no durable model/agent memory for banking state.
- SPIRE is upstream FOSS on bank-controlled EKS workloads; AWS-managed AgentCore/Gateway does not automatically present SVIDs.
- Two distinct Cedar enforcement contexts: Gateway tool authorization and backend banking-resource authorization.
- Private MCP ingress integration and Ping token propagation are **hypotheses requiring a working PoC**.
- OSS Presidio/NeMo/LLM Guard supplement, never replace, deterministic authorization.
- Release signoff requires tests and actual evidence, not just green design documentation.

## Register workflow
1. Add a stable `OI-NNN` row when a material assumption, risk, interface or control is unresolved. Link it from the relevant ADR.
2. A designated owner produces a decision and reproducible proof (PR, test ID, IaC diff, trace or security review). Record the proof link and change status to **Resolved** only after review.
3. Reopen if SDK/service behavior, policy or threat assumptions change. Material irreversible choices require a new/superseding ADR.
4. Do not silently mark issues closed because a document was written. Escalate unresolved P0 entries before the linked phase exit; review P1 items before MVP release.
