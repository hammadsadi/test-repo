# Payment Module API Documentation

## Base URLs

https://lobster-app-74s7m.ondigitalocean.app

- **V1 API**: `/api/v1`

---

This document provides a comprehensive list of all APIs in the Payment module with their input/output schemas.

## Table of Contents

- [Authentication & Authorization](#authentication--authorization)
- [Payment Processing APIs](#payment-processing-apis) (SSLCommerz)
- [Admin Payment APIs](#admin-payment-apis)

---

## Authentication & Authorization

Protected endpoints require authentication via JWT token in the Authorization header:

```
Authorization: Bearer <access_token>
```

### User Roles

- `user` - Regular user
- `workspace_user` - User within a workspace
- `workspace_admin` - Admin of a workspace
- `admin` - System admin
- `super_admin` - Super admin with full access

---

## Payment Processing APIs

These endpoints are primarily used for SSLCommerz callbacks and validation.

### 1. Validate Payment

**Endpoint:** `POST /api/payment/validate`

**Auth Required:** All authenticated users

**Request:**

```json
{
  "val_id": "string (required, validation ID from SSLCommerz)",
  "tran_id": "string (required, transaction ID)"
}
```

**Response:**

```json
{
  "statusCode": 201,
  "success": true,
  "message": "",
  "data": {
    "status": "VALID",
    "tran_date": "2026-02-06 12:00:00",
    "amount": "100.00",
    "store_amount": "97.50",
    "currency": "BDT"
    // ...other SSLCommerz validation data
  }
}
```

---

### 2. Payment Success Callback

**Endpoint:** `POST /api/payment/success`

**Auth Required:** All authenticated users (Used as Callback)

**Request:** (From SSLCommerz)

```json
{
  "val_id": "string",
  "tran_id": "string",
  "amount": "100.00",
  "card_type": "VISA"
  // ...other SSLCommerz POST data
}
```

**Response:**

- Redirects to frontend success page:
  `${FRONT_END_BASE_URL}/payment-success?tran_id={tran_id}`

**Side Effects:**

- Validates payment with SSLCommerz
- Updates `PaymentGatewayDetails` in DB with "paid" status
- Updates `Subscription` userPaymentStatus to "paid"

---

### 3. Payment Failed Callback

**Endpoint:** `POST /api/payment/fail`

**Auth Required:** None (Public Callback)

**Request:** (From SSLCommerz)

```json
{
  "tran_id": "string",
  "error": "Payment failed"
  // ...other SSLCommerz POST data
}
```

**Response:**

- Redirects to frontend failed page:
  `${FRONT_END_BASE_URL}/payment-failed?tran_id={tran_id}`

**Side Effects:**

- Updates `Subscription` userPaymentStatus to "failed"

---

### 4. Payment Cancel Callback

**Endpoint:** `POST /api/payment/cancel`

**Auth Required:** All authenticated users (Used as Callback)

**Request:** (From SSLCommerz)

```json
{
  "tran_id": "string",
  "error": "Payment cancelled"
  // ...other SSLCommerz POST data
}
```

**Response:**

- Redirects to frontend cancel page:
  `${FRONT_END_BASE_URL}/payment-cancel?tran_id={tran_id}`

**Side Effects:**

- Updates `Subscription` userPaymentStatus to "cancelled"

---

### 5. IPN (Instant Payment Notification) Listener

**Endpoint:** `POST /api/payment/ipn`

**Auth Required:** None (Public Listener)

**Request:** (From SSLCommerz)

```json
{
  "val_id": "string",
  "tran_id": "string",
  "status": "VALID"
  // ...other SSLCommerz IPN data
}
```

**Response:**

```json
{
  "statusCode": 201,
  "success": true,
  "message": "",
  "data": {
    // Validation result object
  }
}
```

**Side Effects:**

- Validates payment
- Updates `PaymentGatewayDetails` in DB
- Updates `Subscription` status

---

## Admin Payment APIs

### 6. Get User Payment History

**Endpoint:** `GET /api/payment/user/:userId/history`

**Auth Required:** `admin`, `super_admin`, `user`, `workspace_admin`, `workspace_user`

**URL Parameters:**

- `userId` - The ID of the user

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "User payment history fetched successfully",
  "data": [
    {
      "tran_id": "string",
      "amount": 100,
      "currency": "BDT",
      "status": "paid",
      "card_type": "VISA",
      "createdAt": "2026-02-06T12:00:00Z"
      // ...IPaymentGatewayDetailsSchema properties
    }
  ]
}
```

---

### 7. Get Payment Summary

**Endpoint:** `GET /api/payment/summery`

**Auth Required:** `admin`, `super_admin`

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Payment summery fetched successfully",
  "data": [
    {
      "card_issuer": "Bank Asia",
      "totalTransactions": 150,
      "totalAmount": 50000
    },
    {
      "card_issuer": "Bkash",
      "totalTransactions": 300,
      "totalAmount": 45000
    }
  ]
}
```

---

## Data Types

### PaymentStatus Enum

- `pending`
- `paid`
- `failed`
- `cancelled`
- `VALID`
- `INVALID`

### IPaymentGatewayDetailsSchema

```typescript
{
    userId: string;
    tran_id: string;
    val_id?: string;
    amount: number;
    store_amount?: number;
    currency: string;
    product_name?: string;
    card_type?: string;     // e.g. "Bkash", "Nagad", "VISA"
    card_brand?: string;
    card_issuer?: string;
    card_category?: string;
    card_sub_brand?: string;
    bank_tran_id?: string;
    tran_date?: string;
    validated_on?: string;
    risk_title?: string;
    risk_level?: string;
    discount_percentage?: string;
    discount_amount?: string;
    discount_remarks?: string;
    status: PaymentStatus;
    paymentGatewayData?: any; // Full raw response
    createdAt: Date;
    updatedAt: Date;
}
```
