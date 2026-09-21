# Refund Payment API

Creates a refund request for a previously successful payment transaction.

## Endpoint

POST /v1/payments/{transactionId}/refund

## Description

This endpoint is used by a merchant to request a refund for a completed payment transaction.

A refund should only be processed when the transaction is eligible according to the payment product and business rules.

---

## Request Headers

| Header | Required | Description |
|---|---|---|
| Authorization | Yes | Bearer authentication token |
| Content-Type | Yes | application/json |
| Idempotency-Key | Recommended | Unique key used to prevent duplicate refund processing |

---

## Path Parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| transactionId | String | Yes | Unique transaction identifier |

---

## Request Body

| Parameter | Type | Required | Description |
|---|---|---|---|
| amount | Number | No | Amount to refund |
| reason | String | No | Reason for the refund |
| referenceId | String | No | Merchant reference for the refund |

If `amount` is not provided, the system may process a full refund depending on the configured business rules.

---

## Example Request

    POST /v1/payments/TXN-001/refund

    Content-Type: application/json
    Authorization: Bearer <access-token>
    Idempotency-Key: REFUND-001

    {
      "amount": 150000,
      "reason": "Customer requested refund",
      "referenceId": "REF-001"
    }

---

## Example Response

    {
      "responseCode": "00",
      "status": "REFUNDED",
      "description": "Transaction has been refunded.",
      "transactionId": "TXN-001",
      "refundId": "RF-001",
      "amount": 150000,
      "currency": "IDR"
    }

---

## Response Fields

| Field | Type | Description |
|---|---|---|
| responseCode | String | API response code |
| status | String | Current refund status |
| description | String | Human-readable response description |
| transactionId | String | Original payment transaction identifier |
| refundId | String | Unique refund identifier |
| amount | Number | Refund amount |
| currency | String | Refund currency |

---

## Refund Status

| Status | Description |
|---|---|
| REFUND_PENDING | Refund request has been accepted and is being processed |
| REFUNDED | Refund completed successfully |
| REFUND_FAILED | Refund processing failed |

---

## Response Codes

| Code | Status | Description |
|---|---|---|
| 00 | REFUNDED | Refund completed successfully |
| 10 | REFUND_PENDING | Refund request is being processed |
| 20 | REFUND_FAILED | Refund processing failed |
| 30 | NOT_FOUND | Transaction could not be found |
| 31 | INVALID_STATUS | Transaction is not eligible for refund |
| 32 | INVALID_AMOUNT | Refund amount is invalid |
| 33 | DUPLICATE_REQUEST | Refund request has already been processed |

---

## Refund Eligibility

Before processing a refund, the system should validate:

- Transaction exists
- Transaction belongs to the requesting merchant
- Transaction status is eligible for refund
- Refund amount is valid
- Refund amount does not exceed the refundable amount
- Refund request is not duplicated

---

## Refund Flow

    Merchant
       |
       | Refund Request
       v
    Payment Gateway
       |
       | Validate Transaction
       v
    Transaction Service
       |
       | Check Refund Eligibility
       v
    Payment Provider
       |
       | Refund Result
       v
    Payment Gateway
       |
       | Update Transaction
       v
    REFUNDED

---

## Partial Refund

Some payment products may support partial refunds.

Example:

Original Payment:

    Amount = IDR 150,000

Partial Refund:

    Amount = IDR 50,000

Remaining refundable amount:

    IDR 100,000

The system should prevent the total refunded amount from exceeding the original transaction amount.

---

## Full Refund

Example:

    Original Transaction
          |
          v
       PAID
          |
          | Refund 150,000
          v
      REFUND_PENDING
          |
          v
       REFUNDED

---

## Duplicate Refund Request

The refund API should be idempotent.

Example:

    Refund Request #1
           |
           v
      REFUND-001
           |
           v
    Refund Processing

    Refund Request #2
           |
           v
      Same Idempotency-Key
           |
           v
    Return Existing Refund Result

This prevents an unintended duplicate refund.

---

## Invalid Refund Scenario

Example:

    Transaction Status = FAILED
             |
             v
       Refund Request
             |
             v
      Validate Eligibility
             |
             v
        INVALID_STATUS

The system should not process a refund when the transaction is not eligible.

---

## Refund Transaction Lifecycle

    REFUND_REQUESTED
            |
            v
     REFUND_PENDING
            |
       ┌────┴────┐
       ▼         ▼
   REFUNDED   REFUND_FAILED

---

## Product Considerations

The refund process should consider:

- Full vs partial refund
- Refund eligibility
- Refund limits
- Duplicate requests
- Idempotency
- Refund processing time
- Customer notification
- Merchant reconciliation
- Settlement impact
- Refund audit trail

---

## Operational Considerations

Operational teams may need to investigate:

- Refund stuck in pending
- Refund failed at payment provider
- Duplicate refund request
- Refund amount mismatch
- Merchant reports refund not received
- Refund status mismatch between systems

---

## Audit Information

A refund transaction should be traceable using information such as:

- Transaction ID
- Refund ID
- Merchant ID
- Order ID
- Refund amount
- Refund timestamp
- Refund reason
- Request identifier
- Processing status

---

## Example Customer Journey

    Customer requests refund
             |
             v
         Merchant
             |
             v
      Payment Gateway
             |
             v
      Refund Processing
             |
             v
      Refund Completed
             |
             v
    Merchant updates order
             |
             v
      Customer notified

---

## Disclaimer

This is a portfolio project created for demonstration and educational purposes.

It does not contain production credentials, real customer data, real merchant information, or confidential company information.
