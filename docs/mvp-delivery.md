# MVP Delivery Plan

[Roadmap project](https://github.com/users/srallapally/projects/5). The dates below are **rough estimates, not commitments**.

| Phase | Epic | Delivery | Exit proof |
| --- | --- | --- | --- |
| P0 | [Architecture and contracts](https://github.com/srallapally/acme-bank-agentic-platform/issues/1) | ADRs, trust/transport matrices, MVP contract | Security/platform review |
| P1 | [AWS and SPIRE](https://github.com/srallapally/acme-bank-agentic-platform/issues/2) | IaC, EKS, upstream SPIRE, mTLS pair | Wrong peer rejected |
| P2 | [Ping and Cedar](https://github.com/srallapally/acme-bank-agentic-platform/issues/3) | Token broker, Cedar PDP, deny fixtures | Expired/cross-customer denied |
| P3 | [AgentCore and secure MCP](https://github.com/srallapally/acme-bank-agentic-platform/issues/4) | One agent, Gateway, private MCP adapter | Authenticated tool invocation / bypass denied |
| P4 | [Banking slice](https://github.com/srallapally/acme-bank-agentic-platform/issues/5) | Balance, beneficiary, payment preparation | Durable synthetic payment intent |
| P5 | [PII, AI security, evals](https://github.com/srallapally/acme-bank-agentic-platform/issues/6) | Sanitizers, guardrails, eval suites | Approved regression gates pass |
| P6 | [Hardening and release](https://github.com/srallapally/acme-bank-agentic-platform/issues/7) | Runbooks, budgets, rollback, audit | Kill-switch and recovery exercise |
| P7 | [Post-MVP expansion](https://github.com/srallapally/acme-bank-agentic-platform/issues/8) | Multiple agents, stronger governance, approved posting | Separate production criteria |

## Critical path
P0 → P1 → P2 → P3 → P4 → P6. PII policies, fixtures and tests start in parallel from early phases. P5 is required for release even if implementation overlaps P3/P4.

## Release definition of done
- Valid Ping subject triggers one isolated agent session; no cross-user session reuse.
- Gateway-authorized tool calls reach private MCP service with verified managed-service auth.
- Internal EKS calls use SPIFFE mTLS and deny unauthorized workloads.
- Backend Cedar prevents cross-customer access and unauthorized actions.
- Agent prepares a synthetic payment but cannot commit live funds.
- PII is minimized before inference and sanitized before telemetry/evals.
- Automated security/quality eval reports and audit correlation are retained.
- Rate/cost caps, revocation and rollback are tested.
- Document outstanding exceptions; no unreviewed critical trust assumptions.

## Engineering approach
Small incremental stories: **Action → Verify**. Start with minimal integration proofs, then extend. No test/deployment success claims until evidence exists.
