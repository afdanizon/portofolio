# 💳 Payment Gateway API

A simulated payment gateway portfolio project demonstrating a typical digital payment transaction lifecycle.

This project focuses on payment product concepts, API integration, transaction processing, authentication, callback handling, and settlement.

---

## 🎯 Project Overview

The purpose of this project is to demonstrate how a payment transaction can be designed and documented from the initial payment request until the final transaction status.

The project is designed as a portfolio example and does not represent any production payment system.

---

## 🔄 Payment Flow

The payment lifecycle in this project follows:

Create Payment
↓
Authentication
↓
Payment Processing
↓
Transaction Status
↓
Callback
↓
Settlement

---

## 🏗️ High-Level Architecture

Customer

↓

Merchant

↓

Payment Gateway

↓

Payment Provider / Bank

↓

Transaction Processing

↓

Callback

↓

Merchant

---

## 💰 Create Payment

The merchant sends a payment request to the payment gateway.

Example request:

```json
{
  "merchantId": "DEMO001",
  "orderId": "ORDER-001",
  "amount": 150000,
  "currency": "IDR"
}

## Example response:
{
  "responseCode": "00",
  "status": "PENDING",
  "description": "Payment request has been accepted."
}
