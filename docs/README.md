# ACME Bank — Engineering Documentation

These documents describe the **proposed MVP**; they are not evidence of deployed services or completed tests.

| Document | Purpose |
| --- | --- |
| [Architecture](architecture.md) | System design, data/control planes, trust boundaries, MVP scenario |
| [Technology stack](technology-stack.md) | Languages, libraries, AWS services, standards and trade-offs |
| [Monorepo design](monorepo.md) | Repository layout, contracts, conventions, CI/CD |
| [Threat model](threat-model.md) | Assets, adversaries, attack paths, mitigations |
| [Transport security](transport-security.md) | TLS/mTLS and managed AWS-to-SPIRE boundary |
| [Identity and authorization](identity-authorization.md) | Ping, SPIRE, Cedar and delegated authority |
| [PII and data protection](pii-data-protection.md) | Data classification, minimization, redaction and audit |
| [AI security](ai-security.md) | Guardrails, MCP threats, tool safety and containment |
| [Penetration testing](penetration-testing.md) | Authorized security test plan and acceptance criteria |
| [Evaluations](evaluations.md) | Offline/online evals and deterministic release gates |
| [MVP delivery](mvp-delivery.md) | Phases, critical path and definitions of done |

[Root README](../README.md) · [GitHub Project](https://github.com/users/srallapally/projects/5).

**Notation:** MUST = intended requirement; SHOULD = recommendation; TBD = decision or proof still pending. No real customer data or live payment processing in the MVP.
