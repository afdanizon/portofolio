# Get Transaction Status API

Retrieves the latest status and details of a payment transaction.

## Endpoint

GET /v1/payments/{transactionId}

## Description

This endpoint is used by a merchant to retrieve the latest transaction status from the payment gateway.

It can be used when:

- The merchant has not received a callback
- The payment status is still pending
- The merchant needs to verify the latest transaction status
- The transaction result needs to be checked before fulfilling an order

---

## Request Headers

| Header | Required | Description |
|---|---|---|
| Authorization | Yes | Bearer authentication token |
| Content-Type | Yes | application/json |

---

## Path Parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| transactionId | String | Yes | Unique transaction identifier |

---

## Example Request

    GET /v1/payments/TXN-001

    Authorization: Bearer <access-token>
    Content-Type: application/json

---

## Example Response - PAID

    {
      "responseCode": "00",
      "status": "PAID",
      "description": "Transaction was successful.",
      "transactionId": "TXN-001",
      "orderId": "ORDER-001",
      "amount": 150000,
      "currency": "IDR"
    }

---

## Example Response - PENDING

    {
      "responseCode": "10",
      "status": "PENDING",
      "description": "Transaction is still being processed.",
      "transactionId": "TXN-001",
      "orderId": "ORDER-001",
      "amount": 150000,
      "currency": "IDR"
    }

The `PENDING` status means the payment gateway has not received or finalized the final transaction result.

---

## Example Response - FAILED

    {
      "responseCode": "20",
      "status": "FAILED",
      "description": "Transaction failed.",
      "transactionId": "TXN-001",
      "orderId": "ORDER-001",
      "amount": 150000,
      "currency": "IDR"
    }

---

## Example Response - EXPIRED

    {
      "responseCode": "21",
      "status": "EXPIRED",
      "description": "Transaction has expired.",
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

## Transaction Status

| Status | Meaning | Merchant Action |
|---|---|---|
| PENDING | Transaction is still being processed | Continue waiting or perform inquiry |
| PAID | Transaction completed successfully | Fulfill the order |
| FAILED | Transaction failed | Do not fulfill the order |
| EXPIRED | Transaction expired | Transaction requires a new payment |
| REFUNDED | Transaction was refunded | Update refund status |

---

## Response Codes

| Code | Status | Description |
|---|---|---|
| 00 | PAID | Transaction was successful |
| 10 | PENDING | Transaction is still being processed |
| 20 | FAILED | Transaction failed |
| 21 | EXPIRED | Transaction has expired |
| 30 | NOT_FOUND | Transaction could not be found |

---

## Transaction Inquiry Flow

    Merchant
       |
       | GET /v1/payments/{transactionId}
       v
    Payment Gateway
       |
       | Check Transaction
       v
    Transaction Database
       |
       | Current Status
       v
    Payment Gateway
       |
       | Transaction Result
       v
    Merchant

---

## Pending Scenario

A transaction may remain in `PENDING` when the final status has not yet been received from the payment provider.

Example flow:

    Payment Request
         |
         v
      PENDING
         |
         | No final result yet
         v
    Merchant Inquiry
         |
         v
      PENDING

When the final result is received:

    PENDING
       |
       v
    Provider Result
       |
       +----------------+
       |                |
       v                v
     PAID             FAILED

---

## Callback vs Inquiry

The payment gateway may provide both asynchronous callback and synchronous inquiry mechanisms.

| Mechanism | Purpose |
|---|---|
| Callback | Notify merchant automatically when transaction status changes |
| Inquiry | Merchant actively requests the latest transaction status |

Example:

    Callback Received
         |
         v
    Transaction Updated

If callback is not received:

    Merchant
       |
       | Inquiry
       v
    Payment Gateway
       |
       v
    Latest Transaction Status

---

## Product Considerations

The inquiry API should provide:

- Accurate transaction status
- Consistent response structure
- Clear status descriptions
- Transaction traceability
- Safe handling of unknown transactions
- Protection against inconsistent state updates

---

## Operational Considerations

This API can also be useful for operational investigation.

Common investigation scenarios include:

- Callback not received
- Customer claims payment was successful
- Transaction remains pending
- Payment provider response is delayed
- Merchant order status is inconsistent with payment status
- Reconciliation mismatch

---

## Example Customer Journey

    Customer
       |
       | Makes Payment
       v
    Payment Gateway
       |
       v
    PENDING
       |
       | Provider response delayed
       |
       v
    Merchant performs Inquiry
       |
       v
    Latest Status
       |
       +-------> PAID
       |
       +-------> FAILED
       |
       +-------> EXPIRED

---

## Disclaimer

This is a portfolio project created for demonstration and educational purposes.

It does not contain production credentials, real customer data, real merchant information, or confidential company information.
