# API contract and release status

**Release state: Unavailable / not released.** This repository is the documentation home for the future SMMly customer API. It is not evidence that a live API is currently available.

## Current implementation evidence

The private application repository contains a customer API entry point at `apiconnect/index.php`. In the inspected default-branch revision, that entry point deliberately responds with HTTP `503` and a JSON error before the older handler can execute.

The older, unreachable handler contains legacy SMM V2-style actions named:

- `balance`
- `services`
- `add`
- `status`

These names describe code present behind the early exit, **not currently supported endpoints**. Their request validation, pricing, wallet mutation, provider submission, and error behavior have not been approved as a public contract. They must not be used as integration guidance.

The remediation roadmap also identifies `refill` and `cancel` as future additive actions. They are not documented here as available capabilities.

## Compatibility policy

The product constraint is to preserve existing SMM V2 request and response shapes for actions that are restored. New actions must be additive. Any compatibility statement requires contract fixtures and tests against the released implementation.

## Release gates

The API must remain unavailable until all of the following are complete:

1. Wallet debits, refunds, and ledger attribution use the approved integrity primitives.
2. Provider submission is moved outside the money transaction and unknown submissions cannot be automatically resubmitted.
3. The shared order-submission path is used by both web and API channels.
4. API pricing and discount behavior matches the web order path under the approved owner decision.
5. Authentication, authorization, input validation, abuse controls, rate limits, and safe error handling are implemented and tested.
6. The existing action response shapes are captured as fixtures and verified for compatibility.
7. Refill and cancellation behavior is implemented only with server-side eligibility checks and provider capability evidence.
8. The release is tested in a staging environment and explicitly approved.

The order matters: restoring the old handler before the financial and order-lifecycle prerequisites would expose the same defects through a second entry point.

## Planned contract sections

When release gates are met, this repository will publish:

- Base URL and versioning
- Authentication and API-key lifecycle
- Request encoding and common parameters
- Services, balance, order creation, and order status
- Refill and cancellation, if supported
- Response schemas and error catalogue
- Rate limits, idempotency, retries, and timeout semantics
- Examples and client libraries

## Security

Never commit API keys, provider credentials, bot tokens, customer records, production configuration, or internal audit documents. Report suspected vulnerabilities privately through the process described in the SMMly organization security policy.

## Related repositories

- [SMMly documentation](https://github.com/smmly-co/docs)
- [SMMly examples](https://github.com/smmly-co/examples)
- [SMMly Telegram bot](https://github.com/smmly-co/smmpanel-telegrambot)
