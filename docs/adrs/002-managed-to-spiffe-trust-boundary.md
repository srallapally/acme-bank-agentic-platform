# ADR-002: Managed AgentCore Gateway to SPIFFE-attested EKS services

**Status:** Proposed — blocking integration decision · **Phase:** P0/P3 · **Owner/approvers:** TBD

## Context
AgentCore Gateway is managed AWS infrastructure, not an attested EKS SPIRE workload. A Gateway TLS connection does **not** mean the Gateway possesses an SPIRE-issued client SVID. End-user Ping delegation must survive transport/proxy hops without trusting spoofable headers.

## Decision
Prefer a **private, authenticated MCP ingress** to EKS. Evaluate AgentCore Gateway private MCP target/VPC Lattice connectivity first. At the managed boundary, verify supported outbound authentication, destination server identity, audience and original delegated user context. An EKS ingress adapter with its own SPIFFE identity then uses X.509-SVID mTLS to call Accounts/Payments MCP and OSS Cedar PDP; backend independently authenticates/authorizes user/resource access. Keep Amazon API Gateway as the northbound user channel.

## Alternatives
API Gateway-backed private integration for MCP ingress (fallback if native MCP target does not carry required auth/delegation); direct public MCP (rejected); copying a SPIRE key or SVID into AgentCore (rejected: breaks attestation).

## Consequences
More than one TLS identity boundary exists. Trace which component authenticated the principal and whether context is cryptographically protected. Never treat an ingress workload SVID as proof of original user authorization.

## Verification
Proof-of-concept must show private connectivity, TLS chain/hostname validation, legitimate Ping delegation, forged-header rejection, wrong SVID rejection, unauthorized tool denial and protected backend direct-access denial. Document every TLS termination point and auth mode.

**Open:** [OI-002](../open-issues.md), [OI-003](../open-issues.md), [OI-004](../open-issues.md).
