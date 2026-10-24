# ADR-003 — Ping OAuth delegation

**Status:** Proposed · **Phase:** P0/P2 · **Owners:** IAM + Security (assign) · **Open:** [OI-004](../open-issues.md#oi-004)

## Context
An agent needs narrow authority on behalf of a human. Passing a broad browser token or a refresh token into the model introduces unnecessary risk; multi-hop delegation can inflate authority.

## Decision
PingFederate/PingOne provides OIDC login and OAuth credentials. The backend validates issuer, audience, signature, expiration and authorized client. A trusted token broker performs scoped, audience-specific exchange/delegation where supported by the configured Ping deployment. Agent sessions never hold refresh tokens. Actor, user and task are independently bound; no agent-authored user claims.

## Alternatives
Forwarding the original user bearer token (rejected), static agent credentials (rejected), IAM-only human authorization (not aligned with Ping requirement).

## Consequences
Gateway target outbound credential behavior and backend delegation propagation must be implemented explicitly. Token expiry, cancellation and replay handling must be tested.

## Verification
Prove valid delegation, reduced scopes, expired/wrong audience/issuer denial, actor mismatch denial and non-propagation of refresh tokens. Verify Ping grant configuration and RFC 8693 support actually enabled.

## Open
[OI-004](../open-issues.md#oi-004).
