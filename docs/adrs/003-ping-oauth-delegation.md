# ADR-003: Ping OAuth/OIDC and bounded delegation

**Status:** Proposed · **Phase:** P0/P2 · **Owner/approvers:** TBD

## Context
Agents cannot act as unrestricted service accounts or invent claims about customers. Multi-hop delegation can otherwise expand authority.

## Decision
Use PingFederate/PingOne for customer OIDC and OAuth 2.0. Validate issuer, audience, expiry, client, scopes and relevant actor claims at trust boundaries. Use short-lived audience-specific delegated tokens via RFC 8693 token exchange **if supported by ACME's Ping tenant/configuration**. Broker tokens server-side; no refresh token or client secret in LLM context. Effective rights cannot exceed the intersection of user entitlements, agent capability, delegation and resource permissions. Validate session ownership independently.

## Alternatives
Passing full user tokens into agent prompts (rejected); unconstrained agent service credentials (rejected); custom unsigned actor headers (rejected).

## Consequences
The Gateway target mode and token exchange implementation must be proved together. Expiry does not guarantee immediate revocation: backend/Gateway must check current authority on protected actions.

## Verification
Test valid and invalid issuer/audience/scope, actor mismatch, audience replay, token expiry, scope inflation, stolen session ID and removal of permission mid-task.

**Open:** [OI-003](../open-issues.md), [OI-005](../open-issues.md).
