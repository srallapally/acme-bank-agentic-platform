# ADR-001: Ephemeral AgentCore execution with durable control plane

**Status:** Proposed · **Phase:** P0 · **Owner/approvers:** TBD

## Context
Banking actions outlive reasoning sessions. Ephemeral agents reduce unintended state retention but cannot own financial workflow state.

## Decision
Run one Python/Strands agent in a dedicated Amazon Bedrock AgentCore Runtime session for each MVP task. Bind user, task and session identifiers server-side; enforce TTL, tool/cost budgets and cancellation. Store synthetic payment intents, idempotency records and audit state outside the agent (candidate: DynamoDB). No real-money posting in the MVP. A deterministic controller governs execution and revocation, independently of model decisions.

## Alternatives
Long-lived agents (rejected for MVP: persistent authority/state risk); EKS agent pods (reserve if SPIRE identity on the agent itself becomes mandatory); complex supervisor-agent network (defer).

## Consequences
Session teardown does not guarantee instant downstream credential revocation. Backend and Gateway must re-check authority. Agents cannot rely on transient memory to resume approval workflows.

## Verification
Demonstrate user/session isolation, teardown, session ID replay denial, revoke-mid-task behavior and persistence after runtime termination. Do not assume AgentCore microVM exposes the SPIRE Workload API.

**Open:** [OI-001](../open-issues.md), [OI-008](../open-issues.md).
