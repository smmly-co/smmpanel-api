# SMMly API availability

**Status: Draft / not publicly available.**

SMMly is building a reseller API around the established SMM V2-style request model. The [API v2 draft specification](API.md) describes the target interface and must not be treated as a live production contract.

The endpoint is not currently available for customer integrations. A production release requires implementation, compatibility tests, security review, staging verification, and an explicit release decision.

## Target operations

- Service list
- Add order
- Single and multiple order status
- Account balance
- Create single and multiple refills
- Single and multiple refill status
- Cancel orders

## Release requirements

Before publication as a supported API:

1. Verify every action against the SMMly application.
2. Verify account isolation and API-key authorization.
3. Verify pricing, wallet, refund, and order-state behavior.
4. Verify provider timeouts and unknown submission outcomes cannot cause duplicate orders.
5. Verify refill and cancellation eligibility on the server.
6. Publish request/response fixtures and the final error catalogue.
7. Complete staging tests and publish the production base URL.

Until then, use this repository only to review the draft contract.
