# Architecture Decision Records — Phase 0

ADRs record **proposed** choices until accepted by the named platform/security/business reviewers. Status **Proposed** is not approval and does not imply AWS integration has been tested. Update an ADR's status and date after review; supersede rather than silently rewriting accepted decisions.

| ADR | Decision | Status |
| --- | --- | --- |
| [ADR-001](001-ephemeral-agent-execution.md) | Ephemeral AgentCore execution and durable control plane | Proposed |
| [ADR-002](002-managed-to-spiffe-trust-boundary.md) | AgentCore Gateway → EKS/SPIRE boundary | Proposed; integration-blocking |
| [ADR-003](003-ping-oauth-delegation.md) | Ping OIDC and delegated identity | Proposed |
| [ADR-004](004-cedar-enforcement.md) | Cedar at tool and banking-resource boundaries | Proposed |
| [ADR-005](005-mcp-contracts.md) | MCP domains, tool restrictions and payment preparation | Proposed |
| [ADR-006](006-data-protection.md) | PII minimization and telemetry handling | Proposed |
| [ADR-007](007-evaluation-release-gates.md) | Testing, evaluation and release evidence | Proposed |
| [ADR-008](008-technology-and-monorepo.md) | Python/TypeScript technology and monorepo structure | Proposed |

Tracking: [Open issues and assumptions](../open-issues.md) · [Phase 0 epic](https://github.com/srallapally/acme-bank-agentic-platform/issues/1).

## ADR format
Context → Decision → Alternatives → Consequences → Verification → Open issue IDs. Owners and approval dates are TBD until explicitly assigned.
