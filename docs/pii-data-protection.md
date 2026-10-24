# PII and Data Protection

## Ownership
ACME Data Protection/Security owns classification and masking policies; MCP owners own domain field allowlists; the agent platform owns inference/telemetry interception. Use **OSS Presidio** where text detection is needed, supplemented by deterministic bank-specific recognizers and schema-driven minimization.

## Data flow controls
| Boundary | Required handling |
| --- | --- |
| User message → controller/agent | Identify/reduce unnecessary personal data; approved fields only |
| MCP → agent context | Authorize account/fields first; return stable opaque references, account last4 when enough |
| Agent → Bedrock | Apply context minimization/redaction; avoid secrets and full PAN/SSN |
| Agent response → user | Prevent unauthorized cross-customer disclosure; do not blindly strip approved customer data |
| Trace/log/audit/eval export | Separate sensitive evidence from operational metrics; sanitize **before** exporting |
| Token resolution → core banking | Backend-only, narrowly authorized; never arbitrary agent detokenization |

Example model-visible object:
```json
{"account_ref":"acct_tok_91a8","account_last4":"4382","balance":"12500.00","currency":"USD"}
```

## Data rules
- Define classification tiers and retention separately for prompt, tool payload, telemetry, audit and eval data.
- Keep test fixtures fully synthetic; use canary secrets/PII to detect leaks.
- Field allowlists and deterministic tokenization are primary for structured banking APIs; Presidio is defense-in-depth for unstructured text and may miss or over-redact PII.
- Do not log bearer tokens, SVID private keys, account numbers or raw approval evidence.
- Regional inference routing and logging destinations require security/privacy review.
- Implement legal hold, deletion, data-subject response and breach notification processes for production.

## Verification
Fixture tests for SSNs, account numbers, names/addresses, hidden payloads and authorized disclosures. Assert sanitized model context and trace export; inspect false positives and negatives with human-reviewed samples.
