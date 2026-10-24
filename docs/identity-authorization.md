# Identity, Delegation and Authorization

## Human identity — Ping
Use PingFederate/PingOne OIDC for customer and employee sign-in; narrowly scoped OAuth tokens for Gateway/tool access. Validate issuer, audience, expiry, algorithm and client/actor attributes. Use RFC 8693-style token exchange where the installed Ping configuration supports the required delegation model. No user refresh tokens in ephemeral agents.

**Authority attenuation:** effective authority is the intersection of user entitlement, agent capability, delegated scope and resource authorization. A parent agent cannot grant a child broader authority. The token broker—not the LLM—builds delegation assertions.

## Workload identity — upstream FOSS SPIRE
EKS-hosted MCP, ingress, PDP and backend services use SPIRE-issued X.509-SVIDs. Example identities:
- `spiffe://acmebank.internal/prod/mcp/accounts`
- `spiffe://acmebank.internal/prod/mcp/payments`
- `spiffe://acmebank.internal/prod/security/cedar-pdp`

SPIRE node/workload attestation, controlled registration and SDS rotation provide identity. Accept only expected peer IDs. Managed AgentCore execution is authenticated using supported AWS mechanisms; do not fabricate a SPIRE SVID inside a managed microVM.

## Authorization — Cedar in two places
1. **Managed AgentCore Gateway Cedar:** permission to invoke a named MCP tool, based on its supported principal/action/resource schema and trusted claims.
2. **OSS bank Cedar PDP:** permission to operate on a specific bank resource (account/payment intent). Build policy context from verified identity, EKS peer identity and authoritative banking records, never model-generated identity strings.

The managed Gateway and backend PDP **do not share an identical Cedar schema**. Validate separately. A Gateway allow decision never bypasses the backend deny.

## Policy examples (illustrative, not deploy-ready)
```text
principal = verified Ping customer
action = PreparePayment
resource = account belonging to customer
context = authenticated delegated actor + permitted payments scope
          + expected SPIFFE ID + immutable payment intent status
decision = explicit Cedar permit, with forbid overrides
```

## Revocation and failure
Re-evaluate protected operations at execution; disable sessions and backend abilities on suspension. Set explicit policy freshness/revocation SLOs. Fail closed for financial writes when Ping verification, SPIRE authentication or Cedar PDP is unavailable. No silent fallback to agent judgment.
