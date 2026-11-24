# API release implementation checklist

**Document state: Working checklist — not a release declaration.**  
**API state: Unavailable / not released.**

This checklist turns the current API release gates into evidence that can be reviewed. A checkbox must not be marked complete because code exists; it requires a reproducible test or review artifact from the private application.

## Status vocabulary

- **Not verified** — no current evidence was inspected for this item.
- **In progress** — implementation or verification has started, but acceptance evidence is incomplete.
- **Blocked** — a prerequisite, owner decision, or required capability is missing.
- **Verified** — acceptance evidence exists and has been reviewed against the release criteria.
- **Not applicable** — the owner has explicitly excluded the capability from this release.

Do not infer status from a roadmap entry, a legacy function, or a merged commit alone.

## Gate register

| Gate | Required evidence | Dependency / decision | Status |
|---|---|---|---|
| Financial integrity | Tests proving debit, refund, ledger attribution, idempotency, and rollback behavior across web and API order paths | Approved shared money primitives | Not verified |
| Provider submission lifecycle | Tests proving provider HTTP is outside the money transaction; ambiguous/unknown submissions are not automatically resubmitted | Shared submission state machine | Not verified |
| Shared order path | Web and API use the same order-submission and refund primitives; parity tests cover both channels | Financial integrity and provider lifecycle | Not verified |
| Pricing and discounts | Fixtures and tests proving API/web parity for applicable prices, discounts, and fees | Owner decision D11 | Blocked pending decision/evidence |
| Authentication and abuse controls | Tests for key handling, authorization, validation, rate limits, abuse controls, and safe errors | Security review | Not verified |
| Legacy compatibility | Captured request/response fixtures from the intended supported behavior, verified against the implementation | Existing SMM V2 compatibility requirement | Not verified |
| Refill and cancellation | Server-side eligibility rules, state-transition tests, provider capability evidence, and authorization checks | Owner decision D22 and provider evidence | Blocked pending decision/evidence |
| Staging and release approval | Staging test report, reviewed release notes, rollback plan, and explicit owner approval | All applicable gates above | Not verified |

The D11 and D22 labels refer to owner decisions recorded in the private application's audit material. This public checklist intentionally does not reproduce internal audit documents or private implementation details.

## Contract publication sequence

1. Verify the implementation and the release gates in the private application.
2. Capture compatibility fixtures for each action approved for release.
3. Publish only the request fields, response schemas, errors, and behavior demonstrated by those fixtures.
4. Add authentication, rate-limit, timeout, retry, and idempotency guidance only after implementation tests establish the behavior.
5. Add examples only for released operations.
6. Mark the API released only after staging verification and explicit owner approval.

## Non-negotiable publication rules

- Do not turn legacy action names into endpoint documentation.
- Do not guess a base URL, route, parameter, response field, status value, or error code.
- Do not claim that refill or cancellation is supported until eligibility and provider capability are verified.
- Do not publish secrets, customer data, provider credentials, internal audit files, or production configuration.
- Keep the API marked unavailable while its public entry point is unavailable.
- Preserve existing SMM V2 request/response compatibility only where the released implementation and fixtures prove it.

## Current public status

The API contract status page is the public status source: [CONTRACT-STATUS.md](CONTRACT-STATUS.md). The central documentation page is [docs/api/README.md](https://github.com/smmly-co/docs/blob/main/api/README.md).
