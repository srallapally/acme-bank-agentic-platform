# ADR-004: Two Cedar enforcement points

**Status:** Proposed · **Phase:** P0/P2 · **Owner/approvers:** TBD

## Context
Gateway tool permission is not a banking entitlement. Access to an account or payment intent requires independently verified ownership and state.

## Decision
Use Amazon Bedrock AgentCore Policy/Cedar at Gateway for tool invocation rights, and a self-hosted upstream OSS Cedar PDP on EKS for customer/resource permission. Treat their policy schemas as distinct; version, validate and test separately. Backend PEP constructs principals, resources and context from validated Ping token, trusted workload identity and authoritative bank records—not the agent's request body. Default deny, forbid wins, fail closed for sensitive writes. Reauthorize at transaction execution.

## Alternatives
Only Gateway authorization (rejected: cannot stand in for bank-resource policy); LLM-based policy decisions (rejected); parallel policy languages (defer).

## Consequences
The Rust upstream Cedar engine may be exposed as a narrowly scoped internal PDP to the Python MCP fleet. Synchronization, availability and policy freshness need operational design.

## Verification
Deny cross-customer, wrong tool, forged resource owner, stale/expired grants and policy engine outage. Validate policies against each enforcement schema in CI.

**Open:** [OI-006](../open-issues.md), [OI-007](../open-issues.md).
