# FossaPay Documentation Review

This document tracks the documentation issues identified during the repository review. It is intended to serve as a remediation backlog for the documentation and API teams.

## Environment Note

FossaPay currently operates in production only. Documentation must not instruct users to use non-production modes, placeholder test credentials, or pre-production workflows unless that functionality is introduced and verified.

Until additional environments exist, the documentation should clearly explain:

- That only the production environment is available
- Which API key format is currently supported
- That requests may affect live data or move real funds
- Any safe testing procedure available to integrators
- Recommended minimum transaction amounts and precautions for live testing

## Critical Issues

### 1. Conflicting customer schemas

**Status:** Open

The same `POST /api/v1/customers` endpoint is documented with incompatible request bodies:

- `api-reference/openapi.json` expects `name`, `email`, and `phone`.
- `api-reference/customers/create-customer.mdx` expects `firstName`, `lastName`, `emailAddress`, `mobileNumber`, date of birth, address, city, country, and type.
- `api-reference/customers/index.mdx` uses `name`, `email`, and `phone`.
- `concepts/customers.mdx` uses the expanded schema, but its cURL body is not valid JSON.

Confirm the production request and response DTOs, then use them consistently in the OpenAPI specification, API reference, concepts, guides, and AI-tool examples.

### 2. Unsupported non-production instructions

**Status:** Resolved

The quickstart and authentication pages now describe production-only usage and live keys rather than non-production modes or placeholder credentials.

The docs also now warn that integrations operate against production and document the approved live-testing procedure.

### 3. Unsafe webhook signature examples

**Status:** Open

The Node.js webhook examples parse requests with `express.json()` and calculate the HMAC from `JSON.stringify(req.body)`. Signature verification should use the exact raw request bytes unless the backend explicitly signs a canonicalized JSON representation.

The webhook documentation should define:

- The exact bytes included in the signature
- The signature header name
- The hash algorithm
- Whether the signature is hex, Base64, or prefixed
- Safe constant-time comparison with length validation
- Replay protection, if supported
- A test payload, secret, and expected signature

### 4. Inconsistent webhook contracts

**Status:** Open

Webhook pages disagree on event and field names:

- `payment.received` versus `deposit.completed`
- `event_id` versus `eventId`
- `transaction_id` versus `transactionId`
- `explorer_link` versus `explorerLink`
- Snake case versus camel case generally
- Bearer authentication versus `x-api-key` for webhook configuration

Publish one canonical webhook envelope and event schema, then update every handler and payload example from that source.

### 5. Incomplete quickstart flow

**Status:** Open

The quickstart is not executable from beginning to end:

- It uses a customer ID before creating a customer.
- It jumps from Step 4 to Step 6.
- It promised payment simulation even though production-only live testing is the supported approach.
- It changes event names between examples.
- It does not configure the webhook secret used by its code.
- It does not define monetary units.

Rewrite the flow for the production-only environment. It should create a customer, create a wallet, configure a webhook, explain a safe live deposit test, verify the event, retrieve the transaction, and only then introduce payouts.

## High-Priority Issues

### 7. Incomplete and disconnected OpenAPI specification

**Status:** Open

The OpenAPI document contains only three customer operations, while the MDX documentation covers customers, NGN wallets, crypto wallets, transfers, fees, banks, and webhooks. Its customer schema also conflicts with the dedicated customer reference.

Expand the specification to cover every public endpoint, apply the API-key security scheme, add complete request and response schemas, and connect the specification to the documentation build. Prefer generating endpoint reference pages from this contract where practical.

### 8. Undefined monetary units and precision

**Status:** Open

Examples use values such as `50000`, `1000000`, and `"10.00000000"` without consistently stating whether they represent major units, minor units, or decimal strings. One quickstart example divides an amount by 100 while other examples display it directly.

Add a money and precision guide covering NGN units, stablecoin decimals, accepted JSON types, rounding behavior, fee calculations, currency casing, and webhook/API consistency. Describe the unit on every amount field.

### 9. Non-runnable code examples

**Status:** Open

Known defects include:

- Invalid JSON in the customer concept cURL example
- A missing `os` import in a Python webhook example
- An AI-tool example that accesses `customer.data.id` before parsing the `fetch()` response
- A trailing comma in a webhook JSON payload
- Shell commands inside JavaScript-labelled code blocks

Add automated formatting and syntax checks for JSON, JavaScript, Python, shell, and OpenAPI examples. Run integration examples against a mock contract or another safe verification mechanism.

### 10. Inconsistent response and error envelopes

**Status:** Open

The API reference alternates between boolean `success`, boolean `status`, string `status`, raw arrays, wrapped objects, and unwrapped transaction responses. Error examples also lack a consistent machine-readable shape.

Confirm whether these differences reflect the API. If not, standardize the response envelope. Document HTTP status, stable error code, message, field errors, request ID, and retry guidance.

## Structural and Editorial Issues

### 11. Duplicate landing pages

**Status:** Open

`index.mdx` and `introduction.mdx` contain the same content. Keep one canonical introduction and give the root route a deliberate landing-page role or redirect.

### 12. Non-reproducible local development setup

**Status:** Open

The README describes a generic Mintlify template and relies on an unpinned globally installed CLI.

Replace it with a FossaPay contributor guide that defines the supported Node.js and Mintlify versions, preview and validation commands, repository structure, review workflow, deployment process, and ownership for API contract changes.

### 13. Missing operational documentation

**Status:** Open

Add dedicated documentation for:

- API versioning and deprecation
- Rate-limit headers and retry behavior
- Idempotency scope, retention, and conflicts
- Pagination conventions
- Request IDs and support diagnostics
- Transaction state transitions
- Reconciliation and settlement
- Webhook logs and replay
- Changelog and service status
- Compliance, KYC requirements, and transaction limits

### 14. Regulatory and white-label claims need qualification

**Status:** Open

Claims that FossaPay handles compliance or that customers do not need a banking license are jurisdiction-sensitive. Document applicable countries, eligibility conditions, customer responsibilities, legal caveats, onboarding requirements, and links to authoritative terms.

## Recommended Remediation Order

1. Confirm customer schemas, webhook contracts, amount units, authentication, and all endpoint paths with the backend team.
2. Remove unsupported non-production and placeholder-key instructions.
3. Rewrite the quickstart using the verified production contract and a safe live-testing process.
4. Expand OpenAPI and use it as the API source of truth.
5. Correct and automatically validate all code examples.
6. Remove duplicate pages.
7. Add operational, compliance, and contributor documentation.
