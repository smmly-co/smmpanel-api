# API contract and release status

**Release state: Unavailable / not released.** This repository is the documentation home for the future SMMly customer API. It is not evidence that a live API is currently available.

## Current implementation evidence

The private application repository contains a customer API entry point at `apiconnect/index.php`. In the inspected default-branch revision, the entry point deliberately responds with HTTP `503` and a JSON error before the older handler can execute.

The older, unreachable handler contains legacy SMM V2-style actions named `balance`, `services`, `add`, and `status`. These names describe code present behind the early exit, **not currently supported endpoints**. Their request validation, pricing, wallet mutation, provider submission, and error behavior have not been approved as a public contract.

The private remediation roadmap identifies refill and cancellation as future product/API work, but those capabilities are not available through the current public API. They depend on the order-lifecycle work and provider-specific evidence described below.

## Compatibility policy

The product constraint is to preserve existing SMM V2 request and response shapes for the existing actions that are restored. New actions are additive only. Compatibility claims require byte-level fixtures and tests against the released implementation.

## Restoration prerequisites

The private remediation roadmap's R2-2 task explicitly requires all of the following before the public API is restored:

1. **R1-1:** financial-integrity primitives are implemented and verified.
2. **R1-3:** refund attribution and refund caps are implemented and verified.
3. **R1-4:** provider response classification is implemented and verified.
4. **R2-1:** provider HTTP is outside the money transaction, a shared order-submission writer is used by all call sites, and unknown submissions cannot be automatically or manually resubmitted.
5. **R2-2:** the API `add` path uses the same submission state machine and refund primitive as the web path, with web/API pricing and discount behavior aligned under owner decision D11.

Do not fix the API include path or remove the HTTP 503 containment before R1-1, R1-3, R1-4, and R2-1 are complete. The roadmap explicitly warns that restoring the old handler earlier would reactivate its financial and provider-submission defects.

## Refill, cancellation, and provider behavior

These are not implied by the legacy handler's service fields or action names.

- R2-4 requires canonical order-status handling and server-side eligibility checks for customer cancel/refill actions; it depends on R0-7 and R2-1, with owner decisions D12 and D13.
- R2-5 requires provider-specific status and remains semantics, terminal-state evidence, and refund-safety verification; it depends on R2-1 and R1-3, with owner decisions D2 and D13.
- R2-6 records provider capabilities using provider documentation and authorized read-only `status`/`services` probes. It must not use `add`, `refill`, or `cancel` probes. Owner decision D22 defines provider scope and who authorizes those read-only probes.

## Release gates

The API remains unavailable until its restoration prerequisites, relevant security controls, compatibility tests, provider-dependent requirements, staging verification, and explicit owner release approval are complete.

## Contract publication

Only after release will this repository publish verified details such as:

- Base URL and versioning
- Authentication and API-key lifecycle
- Request encoding and supported parameters
- Supported action request/response schemas
- Error catalogue and safe error behavior
- Rate limits, idempotency, retries, and timeout semantics
- Examples and client libraries

No URL, parameter, response field, error code, or retry behavior is published until verified against the released implementation.

## Security

Never commit API keys, provider credentials, bot tokens, customer records, production configuration, or internal audit documents. Report suspected vulnerabilities privately through the process described in the SMMly organization security policy.

## Related repositories

- [SMMly documentation](https://github.com/smmly-co/docs)
- [SMMly examples](https://github.com/smmly-co/examples)
- [SMMly Telegram bot](https://github.com/smmly-co/smmpanel-telegram-bot)
