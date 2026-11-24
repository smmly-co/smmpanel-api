# API release implementation checklist

**Document state:** Evidence checklist, not a release declaration.  
**API state:** Unavailable / not released.

This checklist follows the private application's remediation roadmap. It is intentionally stricter than a list of desired API features: implementation, test evidence, owner decisions, and release approval are separate requirements.

## Status vocabulary

- **Not verified** — current acceptance evidence has not been inspected.
- **In progress** — implementation or verification has started, but acceptance evidence is incomplete.
- **Blocked** — a prerequisite, owner decision, or required capability is missing.
- **Verified** — reproducible acceptance evidence has been reviewed.
- **Not applicable** — the owner explicitly excludes the capability from this release.

Do not infer completion from a roadmap entry, legacy function, or merged commit alone. Update this register only when the corresponding evidence is available.

## Release sequence

The private roadmap explicitly gates restoration of the public API (R2-2) behind these prerequisites:

1. **R1-1 — financial integrity primitives.** Wallet debit/refund and ledger operations must use the approved integrity model.
2. **R1-3 — refund correctness.** Refunds must be attributable and capped; terminal refund behavior must follow the approved policy.
3. **R1-4 — provider response classification.** Provider outcomes must be classified safely before the shared submission writer relies on them.
4. **R2-1 — shared submission kernel.** Provider HTTP must be outside the money transaction; unknown submissions must not be automatically resubmitted; all order-submission call sites must use the shared writer.
5. **R2-2 — restore the public SMM V2 API without changing existing request/response shapes.** The API `add` path must use the same submission state machine and refund primitive as the web path.

Do not repair the API include path or remove the HTTP 503 containment before these prerequisites land. The private roadmap explains that doing so earlier would reactivate the legacy API's financial and provider-submission defects.

## Gate register

| Gate | Required evidence | Owner decision / dependency | Status |
|---|---|---|---|
| R1-1 financial integrity | Tests for wallet debits, refunds, ledger attribution, idempotency, concurrency, and rollback | D1, D4, D5, D7, D14, D15, D21 as applicable; R1-0 ledger attribution precedes money writes | Not verified |
| R1-3 refund correctness | Per-order refund cap and attribution tests; terminal/refund policy tests | D2, D4, D13, D14; R1-0 precedes R1-3 | Not verified |
| R1-4 provider response classification | Tests distinguishing accepted, rejected, and unknown outcomes; safe timeout behavior | D6, D10 as applicable | Not verified |
| R2-1 shared submission kernel | Crash-window tests; no provider HTTP inside money transactions; one shared writer; `submit_unknown` blocks all automatic and manual resend paths | D3, D6, D9, D22; validate against provider evidence from R2-6 | Not verified |
| R2-2 API restoration | Existing `balance`, `services`, `add`, and `status` compatibility fixtures; API add uses shared submission/refund primitives; web/API price parity; explicit staging verification | R1-1, R1-3, R1-4, R2-1; D11 | Blocked pending prerequisites and owner decision |
| R2-4 customer cancel/refill behavior | Server-side status and eligibility checks; canonical status mapping; refill records participate in sync or feature is disabled | R0-7, R2-1; D12 and D13 | Not verified |
| R2-5 provider status sync | Provider-specific terminal status and remains semantics; repeated sync cannot duplicate refunds; pagination and alphanumeric provider IDs tested | R2-1, R1-3; provider evidence; D2 and D13 | Not verified |
| R2-6 provider capability evidence | Provider-by-provider source-cited evidence for add references, idempotency, status/partial quantity, cancel/refund, and terminal statuses; read-only probes only | D22 defines provider scope and who authorizes read-only probes | Blocked pending D22 and authorized evidence |
| Release approval | Staging test report, compatibility results, reviewed release notes, rollback plan, and explicit owner approval | All applicable gates above | Not verified |

## Owner decisions — exact scope

The private owner-decision register records 24 decisions as open in the inspected revision. The API-relevant decisions include:

- **D1:** monetary rounding mode and precision.
- **D2:** terminal refund policy.
- **D3:** submission sweeper/quarantine policy.
- **D4:** payment-to-order attribution.
- **D5:** opening-balance residual policy.
- **D6:** timeout and recovery behavior.
- **D7:** ledger-backed admin balance changes.
- **D8:** manual reconciliation procedure.
- **D9:** migration policy and shared-hosting waiver.
- **D10:** TLS verification requirement.
- **D11:** discount unification between web and API.
- **D12:** manual retry policy.
- **D13:** cancellation/refund policy.
- **D14:** historical residual handling.
- **D15:** BCMath target-host requirement.
- **D21:** foreign keys versus application-level integrity.
- **D22:** provider scope and authorization for read-only capability probes.

D22 is specifically about which providers are in scope for the evidence pass and who authorizes read-only probes. It is not a blanket approval for live provider mutations. Refill/cancel behavior also depends on the relevant order-state and cancellation/refund decisions, plus verified provider capability.

## Contract publication sequence

1. Complete and verify the private application's release prerequisites.
2. Capture byte-level compatibility fixtures for existing SMM V2 actions before and after restoration.
3. Publish only request fields, response schemas, errors, and behavior demonstrated by the released implementation and fixtures.
4. Publish authentication, rate-limit, timeout, retry, and idempotency details only after implementation tests establish them.
5. Publish refill/cancel only after R2-4 and provider evidence are complete.
6. Add examples and SDKs only for released operations.
7. Mark the API released only after staging verification and explicit owner approval.

## Non-negotiable publication rules

- Do not turn legacy action names into endpoint documentation.
- Do not guess a base URL, route, parameter, response field, status value, error code, or retry behavior.
- Do not fix the API include path or remove the HTTP 503 containment ahead of R1-1, R1-3, R1-4, and R2-1.
- Do not claim refill/cancel support until R2-4 and R2-6 evidence is complete and required owner decisions are approved.
- Do not publish secrets, customer data, provider credentials, internal audit files, or production configuration.
- Preserve existing SMM V2 request/response shapes; new actions are additive only.

## Current public status

The current public status source is [CONTRACT-STATUS.md](CONTRACT-STATUS.md). The central documentation page is [docs/api/README.md](https://github.com/smmly-co/docs/blob/main/api/README.md).
