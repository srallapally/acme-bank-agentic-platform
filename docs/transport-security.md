# Transport Security and Workload Trust

Transport confidentiality and *authorization* are different properties. Encrypt each hop, authenticate actual endpoints and carry original end-user delegation through independently verified mechanisms.

| Hop | Target transport | Authentication / validation | Open proof |
| --- | --- | --- | --- |
| Browser → API Gateway | HTTPS; TLS 1.2 minimum, TLS 1.3 where supported | Ping OIDC; audience/issuer/expiry | Identity and CORS tests |
| Session controller → AgentCore | AWS HTTPS API | SigV4/role permissions; session-to-user binding | IAM deny tests |
| Agent → AgentCore Gateway | HTTPS MCP | Supported JWT/IAM auth and Cedar | Tool deny tests |
| AgentCore Gateway → private EKS ingress | HTTPS/private connectivity | Supported target outbound auth, TLS server verification | **Validate target mode and identity propagation** |
| EKS ingress → MCP | TLS, SPIFFE mTLS | X.509-SVID, exact peer SPIFFE ID allowlist | Bad-SVID test |
| MCP → Cedar PDP/banking API | SPIFFE mTLS | Attested peers + verified Ping subject where required | Spoofed principal test |
| Service → data store | TLS | IAM/DB-scoped credentials, encryption | TLS fail-closed test |
| SPIRE Agent → Server | SPIRE authenticated control plane | Node attestation, trust bundle and server verification | Registration/rotation test |

## Mandatory constraints
1. **Do not assert end-to-end SPIFFE mTLS across AgentCore**: its managed microVM/Gateway is outside our node-local SPIRE Agent control.
2. Treat each TLS termination/load balancer as a trust boundary. Downstream credentials are new credentials, not magically propagated client SVIDs.
3. Candidate MCP connectivity: AgentCore Gateway private MCP target (such as supported VPC Lattice integration) with authenticated EKS ingress. Confirm support, private CA trust, outbound credentials and delegation semantics in a real proof-of-concept.
4. Envoy/SPIRE SDS for internal mTLS; validate trust chain **and** expected peer identity. Rotate certs and trust bundles with overlap.
5. Protect SPIRE Workload API sockets, CA roots and registration admin access; no static private key copies into agent sessions.
6. Apply Kubernetes NetworkPolicies/security groups, DNS/egress allowlists and metadata-endpoint controls. Disable insecure certificate validation shortcuts.
7. Define emergency isolation independent of certificate expiration; a still-valid SVID may require network/policy isolation after compromise.
8. Do not trust forwarded identity headers unless injected by a trusted ingress and protected against client spoofing.

## Test strategy
Invalid chain, expired cert, incorrect hostname, wrong SPIFFE ID, bad Ping audience, expired token, TLS downgrade, direct-backend bypass, CA rollover, and ingress-auth propagation must all have negative tests. Record certificate chain, termination locations, protocol negotiation and decision traces without logging secrets.
