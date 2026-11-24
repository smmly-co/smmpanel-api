# SMMly API v2 — Draft compatibility specification

> **Release status: Draft / not available for production use.** This document describes the target SMM V2-compatible interface SMMly is building toward. It is not a statement that the endpoint is currently enabled. The public API endpoint remains unavailable until the implementation and release checks are complete.

SMMly's target API is designed for reseller panels, automation, bots, and custom applications. The interface uses one HTTPS endpoint, POST requests, an API key, an action name, and JSON responses.

## 1. Base request

| Property | Target |
|---|---|
| Method | `POST` |
| Endpoint | `https://YOUR-SMMLY-DOMAIN/api/v2` |
| Request encoding | Form fields (URL-encoded) |
| Response | JSON |
| Authentication | API key in the `key` field |
| Version | `v2` |

The final production hostname and route will be confirmed when the API is released. Do not send API keys in query strings or publish them in client-side browser code.

Every request includes:

| Field | Required | Description |
|---|---:|---|
| `key` | Yes | Customer API key |
| `action` | Yes | API operation |

## 2. Authentication and errors

A request must authenticate to an active SMMly account and may access only that account's data. Keep API keys private, rotate exposed keys, and use HTTPS.

Errors use a JSON object with an `error` message. The final error catalogue and HTTP status mapping will be fixed by the released implementation.

Example shape:

```json
{
  "error": "Invalid API key"
}
```

Do not build retry logic that blindly repeats an order-creation request after a timeout. A timeout can leave the client uncertain whether an order was accepted. Check the order state or contact support before retrying.

## 3. Operations

### 3.1 Service list

Returns the services visible to the authenticated account.

**Fields**

| Field | Value |
|---|---|
| `action` | `services` |

**Target response**

```json
[
  {
    "service": "101",
    "name": "Example service",
    "type": "Default",
    "category": "Instagram",
    "rate": "1.25",
    "min": "50",
    "max": "10000",
    "dripfeed": false,
    "refill": true,
    "cancel": false
  }
]
```

Rates are expressed per 1,000 units. `min` and `max` are service-specific. Capability flags describe whether the service offers drip-feed, refill, or cancellation; they do not guarantee eligibility for every order.

### 3.2 Add order

Creates an order for a service available to the authenticated account.

**Required fields**

| Field | Description |
|---|---|
| `action` | `add` |
| `service` | SMMly service ID |
| `link` | Public target URL |
| `quantity` | Requested quantity |

**Target response**

```json
{
  "order": 23501
}
```

The order ID is the SMMly order reference. Validate service limits and service-specific input requirements before submission. Never request a social platform password.

#### Optional drip-feed fields

| Field | Description |
|---|---|
| `runs` | Number of delivery runs |
| `interval` | Interval between runs, in minutes |

Drip-feed is available only for services that explicitly expose the capability.

#### Service-specific fields

The target contract may expose additional fields for specialized service types. Examples include:

- `comments`: newline-separated custom comments.
- `keywords`: newline-separated keywords for supported SEO services.
- `username`: username or source account where the selected service requires it.
- `min`, `max`, `posts`, `old_posts`, `delay`, `expiry`: subscription parameters where supported.
- `country`, `device`, `type_of_traffic`, `google_keyword`, `referring_url`: traffic targeting fields where supported.
- `answer_number`: poll answer selection where supported.
- `groups`: newline-separated group targets where supported.

These fields are conditional. The service catalogue and final released reference will define the exact accepted fields for each service type. Do not send a field simply because it appears in this draft.

### 3.3 Single order status

**Fields**

| Field | Description |
|---|---|
| `action` | `status` |
| `order` | One SMMly order ID |

**Target response**

```json
{
  "charge": "1.25000",
  "start_count": "1200",
  "status": "In progress",
  "remains": "350",
  "currency": "USD"
}
```

The released implementation will normalize provider-specific states into documented SMMly order states. Status values and currency behavior must be confirmed against the released contract.

### 3.4 Multiple order statuses

**Fields**

| Field | Description |
|---|---|
| `action` | `status` |
| `orders` | Comma-separated order IDs; target maximum 100 |

**Target response**

```json
{
  "23501": {
    "charge": "1.25000",
    "start_count": "1200",
    "status": "Completed",
    "remains": "0",
    "currency": "USD"
  },
  "23502": {
    "error": "Order not found"
  }
}
```

Each requested ID is represented independently. A per-order error does not imply that every other ID in the batch failed.

### 3.5 Account balance

**Fields**

| Field | Value |
|---|---|
| `action` | `balance` |

**Target response**

```json
{
  "balance": "100.84292",
  "currency": "USD"
}
```

The currency field reflects the account's configured API currency in the released implementation. Do not assume USD or INR without checking the returned value.

### 3.6 Create refill

Requests a refill for an eligible order.

**Fields**

| Field | Description |
|---|---|
| `action` | `refill` |
| `order` | One order ID |

**Target response**

```json
{
  "refill": "1"
}
```

A refill is a replacement workflow for a supported drop within the service's stated coverage window. It is not a refund for an undelivered order. Eligibility is checked server-side.

### 3.7 Create multiple refills

**Fields**

| Field | Description |
|---|---|
| `action` | `refill` |
| `orders` | Comma-separated order IDs; target maximum 100 |

**Target response**

```json
[
  { "order": 23501, "refill": 1 },
  { "order": 23502, "refill": { "error": "Order is not eligible for refill" } }
]
```

### 3.8 Refill status

**Fields**

| Field | Description |
|---|---|
| `action` | `refill_status` |
| `refill` | One refill ID |

**Target response**

```json
{
  "status": "Completed"
}
```

### 3.9 Multiple refill statuses

**Fields**

| Field | Description |
|---|---|
| `action` | `refill_status` |
| `refills` | Comma-separated refill IDs; target maximum 100 |

**Target response**

```json
[
  { "refill": 1, "status": "Completed" },
  { "refill": 2, "status": "Rejected" },
  { "refill": 3, "status": { "error": "Refill not found" } }
]
```

### 3.10 Cancel orders

Requests cancellation for one or more orders that are still eligible.

**Fields**

| Field | Description |
|---|---|
| `action` | `cancel` |
| `orders` | Comma-separated order IDs; target maximum 100 |

**Target response**

```json
[
  { "order": 23501, "cancel": 1 },
  { "order": 23502, "cancel": { "error": "Order cannot be canceled" } }
]
```

Cancellation is not guaranteed. The server checks the current order state and service/provider capability. A successful request acknowledgement is not the same as a completed cancellation; check the order status afterward.

## 4. Target status vocabulary

The API aims to expose a stable set of customer-facing states:

| Status | Meaning |
|---|---|
| Pending | Accepted and waiting to begin |
| In progress | Delivery has started |
| Processing | A service-specific processing state |
| Completed | The requested delivery is complete |
| Partial | Delivery ended before the full quantity; eligible unused value is credited according to the order's final accounting |
| Canceled | The order was canceled and its final accounting was applied |
| Error | An order could not be processed; the final response behavior is implementation-defined |

Provider-specific states may be mapped into these values. The final mapping is part of the released contract.

## 5. Order and balance rules

- Service rates are quoted per 1,000 unless the service explicitly states otherwise.
- The final order charge is calculated from the selected service, quantity, and applicable account pricing rules.
- The server is authoritative for price, limits, account balance, and order eligibility.
- Partial or canceled orders must be reconciled through the account ledger. Clients must not calculate or issue refunds themselves.
- Panel balance is account credit for orders; it is not a cash-withdrawal promise.
- Never assume refill or cancellation is available from an order's status alone. Check service capability and server-side eligibility.

## 6. Request examples

### cURL — service list

```bash
curl -X POST "https://YOUR-SMMLY-DOMAIN/api/v2" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  --data-urlencode "key=YOUR_API_KEY" \
  --data-urlencode "action=services"
```

### cURL — add order

```bash
curl -X POST "https://YOUR-SMMLY-DOMAIN/api/v2" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  --data-urlencode "key=YOUR_API_KEY" \
  --data-urlencode "action=add" \
  --data-urlencode "service=101" \
  --data-urlencode "link=https://example.com/public-post" \
  --data-urlencode "quantity=1000"
```

### cURL — check status

```bash
curl -X POST "https://YOUR-SMMLY-DOMAIN/api/v2" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  --data-urlencode "key=YOUR_API_KEY" \
  --data-urlencode "action=status" \
  --data-urlencode "order=23501"
```

Replace the placeholder host and credentials only after SMMly publishes an active API endpoint and issues an API key.

## 7. Compatibility and release policy

SMMly is targeting the established SMM V2-style action and field conventions for the operations listed here. Compatibility is not guaranteed by this draft. The released API will publish exact request fields, response fixtures, error behavior, currency rules, rate limits, and supported service types.

**Do not use this draft as a production integration contract.** The public endpoint is not yet released.
