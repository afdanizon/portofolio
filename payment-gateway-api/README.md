
# 💳 Payment Gateway API

## Overview

A simulated payment gateway designed to demonstrate a typical payment transaction lifecycle.

## Payment Flow

Create Payment
↓
Authentication
↓
Authorization
↓
Callback
↓
Transaction Status
↓
Settlement

## Transaction Status

| Status | Description |
|---|---|
| PENDING | Transaction is still being processed |
| PAID | Payment successfully completed |
| FAILED | Payment failed |
| EXPIRED | Payment session expired |
| REFUNDED | Payment was refunded |

## Example Request

```json
{
  "merchantId": "DEMO001",
  "orderId": "ORDER-001",
  "amount": 150000,
  "currency": "IDR"
}
