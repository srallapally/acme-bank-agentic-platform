# Authorized Penetration Testing Plan

**Scope:** ACME Bank *synthetic MVP* in a dedicated non-production AWS environment only. Written authorization and a rules-of-engagement (ROE) document are required before active testing. No real customer data, real funds, production Ping tenants, or third-party systems without express approval.

## Preparation and safeguards
- List account IDs, AWS regions, VPCs, DNS endpoints, repositories and approved test windows.
- Obtain named security owner, emergency stop contact, allowed traffic rates and tooling approvals.
- Use synthetic OAuth subjects, test-only SPIRE trust domain/registrations, dummy PAN/account values, isolated payment simulator and test budgets.
- Capture security logs; avoid saving tokens, private keys or sensitive raw payloads in findings.
- Coordinate any AWS policy requirements for intrusive testing; avoid destructive load or scanning of managed shared AWS planes.

## Test streams and acceptance criteria
| Area | Test objectives | Expected result |
| --- | --- | --- |
| OAuth/OIDC | Expired, wrong issuer/audience, altered claims, replay, downgrade, scope inflation | Rejected |
| Delegation | Supervisor → tool token laundering, actor mismatch, stolen session ID | Denied and logged |
| SPIFFE/mTLS | Wrong SVID, untrusted root, hostname mismatch, missing peer ID allowlist, expired SVID | Rejected |
| Network/TLS | Gateway bypass, insecure endpoint, egress to non-allowlisted hosts, metadata/SSRF | Blocked |
| Cedar | Cross-customer, forbidden tool, stale grant, malicious context injection, policy outage | Deny/fail closed |
| MCP | Tool description poisoning, unauthorized `tools/call`, malicious result, schema violation, session fixation | No unauthorized action |
| LLM | Direct/indirect injection, jailbreak attempts, tool-loop abuse, prompt secrets extraction | No critical invariant violation |
| PII | Model prompt, logs, trace export and eval corpus with canary data | No forbidden disclosure |
| Banking integrity | Concurrent replay/idempotency, cancellation race, forged approval and wrong beneficiary | No unauthorized state change |
| Supply chain | Dependency tampering, unsigned image, leaked secret in CI, rogue policy revision | Blocked by admission/review |
| Resilience | SPIRE/PDP outage, gateway timeout, kill-switch mid-task, certificate rotation | Defined fail-closed/recovery behavior |

## Execution
1. Threat-model review and ROE approval.
2. Static validation (Terraform, code, image, dependency and policy scans).
3. Dynamic API/MCP auth tests in isolated environment.
4. SPIFFE/TLS negative testing, network isolation checks.
5. Adversarial agent/LLM tests with synthetic content.
6. Failure and recovery exercises; retest after fixes.
7. Evidence report: case ID, asset, reproduction (sanitized), risk, owner, fix, retest result.

## Severity and release gating
- **Block release:** demonstrated cross-customer data read, unauthorized transaction/state write, privilege escalation, token/secret exposure or Gateway/authorization bypass.
- High/medium findings require explicit risk owner, remediation plan and time-bounded exceptions approved under ACME policy.
- An absence of findings does **not** prove security. Report tested coverage, exclusions, limits, and residual risks.

This is a **test plan**, not a pentest result or authorization to attack any live system.
