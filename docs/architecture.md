# Reference Architecture

## Objective and MVP use case
ACME Bank builds a security-first agentic platform that lets an authenticated customer retrieve a synthetic account balance and **prepare** a synthetic payment to an existing beneficiary. The MVP never posts a live payment. Agents are ephemeral reasoning workers, not authorization authorities, transaction processors, or systems of record.

## Logical components
```text
Customer UI (React) -> API Gateway -> Session Controller / Ping token broker
                                            |
                                            v
                            Bedrock AgentCore Runtime (ephemeral Python agent)
                                            |
                              pre-inference data minimization
                                            |
                                            v
                           AgentCore Gateway + managed Cedar tool policy
                                            |
                             private, authenticated MCP target (TBD)
                                            |
                        EKS ingress adapter / verified delegation boundary
                                            |
                                 SPIFFE X.509-SVID mTLS
                                            |
                     +----------------------+------------------+
                     |                      |                  |
                Accounts MCP           Payments MCP       OSS Cedar PDP
                     |                      |                  |
                     +----------- Synthetic banking APIs -----+
                                            |
                            Durable payment intent / idempotency

Cross-cutting: Presidio, OSS AI security, OTel, sanitized audit, evals.
```

## Planes
- **Execution:** AgentCore Runtime isolates tasks and hosts the Python agent; MCP servers execute bounded tools.
- **Governance:** Ping validates user authority; SPIRE authenticates bank-controlled workloads; Cedar evaluates tool and banking-resource authorization; an independent task controller handles quotas, revocation, task-to-user bindings.
- **Assurance:** pytest/security contracts, AgentCore Evaluations, threat modeling, trace correlation and immutable evidence.

## Trust boundaries
1. **Public → AWS:** HTTPS, Ping/OIDC and API Gateway authorization; do not trust unverified headers.
2. **Session controller → managed agent runtime:** AWS-authenticated invocation and session-to-user binding. Managed AgentCore runtime is **not** assumed to support the SPIRE Workload API.
3. **Agent → AgentCore Gateway:** supported Gateway auth, audience-limited delegation and managed Cedar policy; tool discovery does not grant authorization.
4. **AgentCore Gateway → private MCP ingress:** exact private target, TLS, server identity and outbound authentication scheme **must be tested**; AgentCore does not automatically present a SPIRE SVID.
5. **Ingress → EKS MCP / PDP / backend:** independently authenticated SPIFFE mTLS plus service-to-service SPIFFE ID allowlists.
6. **MCP → banking resources:** OSS Cedar checks verified subject, action, resource and context. Never accept agent-generated identity claims as authority.

## Key rules
- One user/task per ephemeral runtime session in MVP; the controller enforces ownership, expiry and cancellation. Agent memory is not durable payment state.
- Separate `prepare_payment` from any eventual `commit_payment`. Persist immutable intent, idempotency key and approval state outside the agent. Commit remains post-MVP.
- Domain MCP tools have strict JSON Schema and server validation. Restrict tool availability by role, risk and customer.
- Fail closed on critical auth/policy failures; apply explicit timeouts, retries, quotas and rate limits.
- Sanitized telemetry and eval artifacts must never become an uncontrolled secondary PII store.

## Open architecture proofs
- Select and test AgentCore Gateway private EKS target mode (e.g. VPC Lattice or authenticated API ingress) with working end-user delegated context.
- Verify TLS termination, host identity, backend authenticated forwarding and anti-spoofing mechanisms.
- Define session revocation SLO, Ping token exchange actor model, exact Cedar schemas, and deployment-region service support.

See [transport](transport-security.md), [identity](identity-authorization.md) and [MVP](mvp-delivery.md).
