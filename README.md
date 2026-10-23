# ACME Bank Agentic Platform

> **Status:** Architecture-defined; MVP implementation planned. This repository is not a production banking system.

A security-first reference platform for **ephemeral banking agents** on AWS. Agents orchestrate approved capabilities through MCP; deterministic services retain responsibility for authorization, durable business state, approvals, and financial effects.

[Project roadmap](https://github.com/users/srallapally/projects/5) · [Architecture epic](https://github.com/srallapally/acme-bank-agentic-platform/issues/1)

## MVP objective

Demonstrate one end-to-end journey using **synthetic banking data**:

1. A customer authenticates through Ping OAuth/OIDC.
2. An ephemeral Amazon Bedrock AgentCore Runtime session retrieves the customer's checking balance.
3. The agent finds an existing beneficiary and **prepares**, but does **not execute**, a $500 payment.
4. AgentCore Gateway authorizes MCP tool use with Cedar; the banking MCP backend separately checks account-level authorization.
5. The pending payment intent survives agent teardown and can be inspected and audited.

**Out of scope for MVP:** live payment posting, unrestricted agent-to-agent delegation, production customer data, a full banking service mesh, and multi-region deployment.

## Logical architecture

```text
Web / Mobile / Employee
          |
     Ping OAuth/OIDC
          |
 Amazon API Gateway (entry point)
          |
 Agent Session Controller / Delegation Broker
          |
 Bedrock AgentCore Runtime (ephemeral task session)
          |          + pre-inference minimization / OSS AI safeguards
          |
 AgentCore Gateway + Cedar tool policies
          |
 private, authenticated MCP target connection
          |
 EKS ingress adapter (verify managed-service identity/delegation)
          |
    SPIFFE mTLS (FOSS SPIRE)
          |
 EKS domain MCP: Accounts / Payments
          |            |
     OSS Cedar PDP    PII filtering
          |
 Synthetic Banking APIs / Durable Payment Intent Store

Cross-cutting: OpenTelemetry, audit events, redaction, evals, release controls
```

The exact **AgentCore Gateway → private EKS MCP → SPIRE** integration is a Phase 3 proof point, not an already verified implementation. VPC Lattice and an authenticated ingress adapter are candidate designs; verify AWS target-mode support, TLS termination, authentication, and user/actor claim propagation before selecting one.

## Technology and responsibility matrix

| Concern | Selected approach | Responsibility |
| --- | --- | --- |
| Cloud/API front door | AWS + Amazon API Gateway | Public-facing HTTPS/API management |
| Agent compute | Bedrock AgentCore Runtime | Short-lived isolated sessions; no durable agent-owned banking state |
| Tool exposure | AgentCore Gateway + MCP | Curated domain tools and gateway policy checks |
| Human identity | PingFederate/PingOne OAuth 2.0/OIDC | User authentication, scopes, and delegation/token exchange |
| Workload identity | **Upstream FOSS SPIRE** on EKS, SPIFFE X.509-SVID | Attest bank-controlled workloads and establish mTLS identity |
| Tool authorization | AgentCore Cedar policy | Deny-by-default MCP tool permissions |
| Resource authorization | Self-hosted **OSS Cedar** PDP on EKS | User/account/resource entitlements at the backend |
| Banking workflows | Durable payment-intent state; Step Functions when needed | Approvals, retries, reconciliation; never rely on runtime memory |
| PII protection | Schema-based minimization + OSS Presidio | Pre-inference, tool-result, outbound, and telemetry filtering |
| AI security | OSS NeMo Guardrails + LLM Guard; deterministic validations | Prompt-injection mitigation, data leakage checks, output constraints |
| Evaluation | AgentCore Evaluations + deterministic tests + OSS adversarial tooling | Quality, trajectory, safety and release gates |
| Observability | OpenTelemetry + AWS logging/audit controls | Traceability without indiscriminate PII logging |
| Infrastructure | Terraform + Kubernetes manifests/Helm | Repeatable environments, least privilege, policy testing |

**Security boundaries:** Ping proves *who the user is*; SPIRE attests *which bank-controlled workload is calling*; Cedar decides *whether a specific action is permitted*. Neither TLS nor an LLM-generated assertion grants banking authorization. Managed AgentCore runtime instances are not assumed to host a SPIRE Agent or possess SPIRE-issued SVIDs.

## Non-negotiable design principles

- **Agents are untrusted planners, not transaction authorities.** Business writes run through independently enforced APIs and durable state machines.
- **One task, one session for MVP.** Do not reuse runtime session identifiers across users/tasks; enforce TTL, concurrency, and revocation at the controller and backend.
- **Zero implicit delegation.** Short-lived, audience-bound Ping tokens; narrow scopes per tool; do not pass refresh tokens to the agent.
- **Default deny.** Tool permissions enforced at Gateway; resource permissions re-evaluated by the bank backend. Policy context comes from verified claims and workload evidence, not user- or model-supplied identity fields.
- **SPIFFE mTLS on bank-controlled east-west hops.** Validate certificate chains *and* authorized peer SPIFFE IDs. AWS-managed-to-EKS hops require their own explicit trust design.
- **Data minimization before inference.** Tokenize/mask account identifiers where possible; redact logs and eval traces before export; never silently allow detokenization.
- **Payment preparation is not payment execution.** Approval and commit require immutable plan binding, current authorization, idempotency, and reconciliation.
- **All external content is untrusted.** MCP responses, retrieved text, tool descriptions and documents cannot override system policy.
- **Release evidence is mandatory.** Model, prompt, policy, image, tool schema and eval dataset versions must be reproducible and auditable.

## Delivery phases

The [GitHub Project](https://github.com/users/srallapally/projects/5) tracks these roadmap epics:

| Phase | Epic | Primary exit criterion |
| --- | --- | --- |
| P0 | [Architecture & contracts](https://github.com/srallapally/acme-bank-agentic-platform/issues/1) | Threat model, trust boundaries, contracts and approval |
| P1 | [AWS & SPIRE foundation](https://github.com/srallapally/acme-bank-agentic-platform/issues/2) | Two EKS workloads communicate with SPIFFE mTLS; wrong identities denied |
| P2 | [Ping identity & Cedar](https://github.com/srallapally/acme-bank-agentic-platform/issues/3) | Valid delegation succeeds; expired/cross-customer/forged requests denied |
| P3 | [AgentCore & secure MCP](https://github.com/srallapally/acme-bank-agentic-platform/issues/4) | Private agent-to-MCP call with explicit authenticated trust handoffs |
| P4 | [Banking vertical slice](https://github.com/srallapally/acme-bank-agentic-platform/issues/5) | Authorized balance/beneficiary read and durable payment preparation |
| P5 | [PII, AI security & evals](https://github.com/srallapally/acme-bank-agentic-platform/issues/6) | Redaction, quality tests and adversarial/regression gates |
| P6 | [Hardening & MVP release](https://github.com/srallapally/acme-bank-agentic-platform/issues/7) | Revocation, recovery, audit reconstruction and release review |
| P7 | [Multi-agent expansion](https://github.com/srallapally/acme-bank-agentic-platform/issues/8) | Separately approved post-MVP capabilities |

Phases are ordered by dependency, not necessarily by team scheduling. Policy tests, threat modeling, banking simulators and evaluation cases should begin early and develop in parallel.

## MVP verification gates

Do not call the MVP complete without recorded evidence that:

1. **Transport:** private MCP traffic is encrypted, endpoints are authenticated, and unauthorized SPIFFE IDs fail; each TLS termination/re-authentication hop is documented.
2. **Identity:** Ping issuer/audience/expiry/delegation are validated; session IDs are bound to the authenticated requester.
3. **Authorization:** gateway tool policy and backend Cedar deny unauthorized tool/account combinations, including cross-customer requests.
4. **Financial integrity:** prepared payment intents are durable and idempotent; no live ledger posting exists in the MVP.
5. **PII:** restricted fields are minimized before model inference and absent from ordinary telemetry/evaluation exports.
6. **Security:** prompt-injection and tool-response attacks cannot bypass independent authorization controls.
7. **Evals:** golden, negative/security and fault-injection suites run with versioned results; no known critical invariant failure is waived.
8. **Operations:** budgets, bounded retries, kill switch, rollback and audit correlation are demonstrated.

These are **acceptance criteria**, not claims of completed tests or deployment.

## Suggested repository structure

```text
architecture/       # ADRs, threat model, transport and delegation matrices
infrastructure/     # Terraform, EKS manifests, Helm/SPIRE
identity/           # Ping integration, SPIRE registration and trust contracts
authorization/      # Cedar schemas, policies, self-hosted PDP and tests
agents/             # Ephemeral runtime entry point and agent logic
mcp/                # Accounts and payments domain tools
banking-simulator/  # Synthetic core banking data and payment state machine
ai-security/        # Presidio, guardrails and tool content validation
evaluations/        # Golden datasets, adversarial cases, release gates
observability/      # Trace, audit and sanitized telemetry configuration
tests/              # Contract, integration, security and recovery tests
```

This tree describes the intended layout. **Directories and implementations are not implied to exist yet.** Build the smallest verifiable vertical slice before introducing additional services.

## Initial engineering workflow

1. Start with [P0](https://github.com/srallapally/acme-bank-agentic-platform/issues/1): approve the trust-boundary diagram, data classes and tool contracts.
2. Implement P1/P2 identity and authorization fixtures with synthetic principals and services.
3. Prove P3 managed Gateway-to-SPIRE connectivity, including negative authentication tests.
4. Implement P4 balance lookup and payment preparation, then introduce P5/P6 assurance and operations.

For every story: **Action → Verification → Evidence**. Keep changes scoped; check correctness, security, edge cases and possible privilege expansion before merging. Avoid recording secrets, real bank data, or OAuth tokens in repository files or CI logs.

## Security and compliance note

This repository is a reference architecture for evaluation. It does not assert regulatory certification, financial production readiness, formal security review, or successful integration tests. Production use requires independent risk assessment, operational readiness, privacy review and verified cloud-service capabilities.
