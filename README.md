# SMMly API

The SMMly API is being built for resellers, custom panels, bots, and automation.

> **Status: Draft / not released.** This repository documents the target SMM V2-compatible interface. The production endpoint is not currently available.

## Documentation

- [API v2 draft specification](API.md) — operations, fields, response shapes, order states, and examples.
- [Release status](CONTRACT-STATUS.md) — public availability notice.

## Target capabilities

The target API covers:

- Service catalogue
- Order creation
- Single and bulk order status
- Account balance
- Refill creation and refill status
- Cancellation requests

The exact production endpoint, supported service types, request validation, response mapping, and error behavior will be confirmed with the released implementation.

## Request model

The target interface uses HTTPS POST, an API key in the `key` field, an `action` field, and JSON responses. See [API.md](API.md).

## Important

Do not use the draft host placeholder as a live endpoint. Do not embed API keys in public repositories, browser code, screenshots, or support requests. Do not blindly retry order creation after a timeout.
