# Technology Stack and Decisions

**Primary languages:** Python 3.12+ for agents/backend/MCP/security tests; TypeScript for browser UI. Introduce Rust only for a small upstream OSS Cedar PDP if needed; do not duplicate banking business logic across languages.

| Component | Selection | Rationale / caveat |
| --- | --- | --- |
| Agent | Python, Strands Agents SDK | Lightweight AgentCore/Bedrock integration; pin compatible versions |
| MCP servers | Python, FastMCP, Pydantic | Typed, testable tool contracts |
| Backend controllers and simulator | Python, FastAPI, Pydantic | ASGI, typed validation, OpenAPI |
| Python tooling | uv, pytest, Ruff, mypy | Reproducible env and automated checks |
| Web UI | TypeScript, React, Vite | Small MVP demo frontend |
| UI libraries | Tailwind, TanStack Query, Vitest | Styling, server state and testing |
| Agent runtime / inference | Bedrock AgentCore Runtime, Amazon Bedrock | Ephemeral managed execution and model access |
| MCP control | Bedrock AgentCore Gateway | MCP tool aggregation and managed Cedar policy |
| Public APIs | Amazon API Gateway | HTTPS API ingress; not necessarily repeated before MCP |
| Workload platform | Amazon EKS, Helm, Envoy | Host controllable MCP, PDP and SPIRE workloads |
| Human IAM | PingFederate/PingOne | OIDC, OAuth 2.0, narrowly scoped delegated tokens |
| Workload IAM | Upstream FOSS SPIRE/SPIFFE | X.509-SVID identity, SDS rotation, internal mTLS |
| Authorization | Cedar; AgentCore Policy and OSS Cedar PDP | Distinct policy schemas, common authorization principles |
| Durability | DynamoDB | Conditional writes/idempotency for synthetic payment intents |
| Workflow | Step Functions Standard if approval/resumption needed | Keep out until the MVP requires durable orchestration |
| PII | OSS Presidio + explicit field allowlists | Classify and minimize before LLM context and telemetry |
| AI security | OSS NeMo Guardrails, LLM Guard | Defense in depth, **not** entitlement checks |
| Eval | AgentCore Evaluations, pytest, DeepEval, promptfoo | Quality + deterministic/adversarial gates |
| Telemetry | OpenTelemetry, CloudWatch | Correlation and sanitized diagnostics |
| IaC / CI | Terraform, Docker, Helm, GitHub Actions | Reviewable infra and release automation |

## Conventions
- OpenAPI for REST, JSON Schema for MCP payloads; generate TypeScript clients rather than manually mirroring Python DTOs.
- Pin dependencies and base image digests; image scanning, SBOM and signing before release.
- Separate agent capability checks from backend banking-resource checks.
- The AWS managed Cedar Gateway schema is not the same as the custom bank Cedar schema.
- No blanket preference for new frameworks: implement one vertical slice before adding distributed orchestration, a mesh or additional agent frameworks.

**Validation required:** Current supported SDK versions, AWS region and target integration capability, private connectivity, policy/schema support, token exchange and cert delivery. A chosen stack is not evidence it has been deployed.
