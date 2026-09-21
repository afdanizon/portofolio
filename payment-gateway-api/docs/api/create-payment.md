# Create Payment API

Creates a new payment transaction.

## Endpoint

POST /v1/payments

## Description

This endpoint is used by a merchant to create a new payment transaction through the payment gateway.

The transaction is initially created with `PENDING` status when the request is accepted for processing.

---

## Request Headers

| Header | Required | Description |
|---|---|---|
| Content-Type | Yes | application/json |
| Authorization | Yes | Bearer authentication token |
| Idempotency-Key | Recommended | Unique key used to prevent duplicate payment creation |

---

## Request Body

| Parameter | Type | Required | Description |
|---|---|---|---|
| merchantId | String | Yes | Unique merchant identifier |
| orderId | String | Yes | Unique order identifier from merchant |
| amount | Number | Yes | Payment amount |
| currency | String | Yes | Transaction currency |
| description | String | No | Payment description |
| customerEmail | String | No | Customer email address |
| returnUrl | String | No | URL to redirect customer after payment |

---

## Example Request

    POST /v1/payments

    Content-Type: application/json
    Authorization: Bearer <access-token>
    Idempotency-Key: ORDER-001

    {
      "merchantId": "DEMO001",
      "orderId": "ORDER-001",
      "amount": 150000,
      "currency": "IDR",
      "description": "Demo Payment",
      "customerEmail": "customer@example.com",
      "returnUrl": "https://merchant.example.com/payment/result"
    }

---

## Example Response

    {
      "responseCode": "00",
      "status": "PENDING",
      "description": "Payment request has been accepted.",
      "transactionId": "TXN-001",
      "orderId": "ORDER-001",
      "amount": 150000,
      "currency": "IDR"
    }

---

## Response Fields

| Field | Type | Description |
|---|---|---|
| responseCode | String | API response code |
| status | String | Current transaction status |
| description | String | Human-readable response description |
| transactionId | String | Unique transaction identifier |
| orderId | String | Merchant order identifier |
| amount | Number | Transaction amount |
| currency | String | Transaction currency |

---

## Response Code

| Code | Status | Description |
|---|---|---|
| 00 | PENDING | Payment request accepted |
| 01 | FAILED | Invalid payment request |
| 02 | FAILED | Invalid amount |
| 03 | FAILED | Duplicate order |
| 04 | FAILED | Authentication failed |

---

## Validation Rules

The API should validate the following:

### merchantId

Must be provided and registered in the system.

### orderId

Must be unique for the merchant.

### amount

Must be greater than zero.

### currency

Must use a supported currency.

---

## Idempotency

The API should support idempotent payment creation.

When the same `Idempotency-Key` or business transaction identifier is submitted multiple times, the system should prevent unintended duplicate transactions.

Example:

    First Request
        ↓
    Create ORDER-001
        ↓
    Transaction TXN-001

    Second Request
        ↓
    Same Idempotency-Key
        ↓
    Return Existing Transaction

---

## Possible Business Scenarios

### Successful Request

    Request
      ↓
    Validation
      ↓
    Create Transaction
      ↓
    PENDING

### Duplicate Order

    Request
      ↓
    Validation
      ↓
    Existing Order Found
      ↓
    Reject Duplicate Request

### Invalid Amount

    Request
      ↓
    Validation
      ↓
    Amount Invalid
      ↓
    FAILED

---

## Product Considerations

The API design should consider:

- Idempotency
- Transaction uniqueness
- Input validation
- Authentication
- Error handling
- Timeout handling
- Transaction traceability
- Clear response messages
- Consistent transaction status

---

## Security Considerations

Sensitive authentication credentials should never be exposed in logs, source code, or API responses.

Recommended practices include:

- Token-based authentication
- HTTPS
- Request validation
- Sensitive data masking
- Secure credential storage

---

## Example End-to-End Flow

    Merchant
       |
       | POST /v1/payments
       v
    Payment Gateway
       |
       | Validate Request
       v
    Transaction Service
       |
       | Create Transaction
       v
    PENDING
