# Subscription Module API Documentation

## Base URLs

https://lobster-app-74s7m.ondigitalocean.app

- **V1 API**: `/api/v1`

---

This document provides a comprehensive list of all APIs in the Subscription module with their input/output schemas.

## Table of Contents

- [Authentication & Authorization](#authentication--authorization)
- [Subscription Management](#subscription-management)
- [Admin APIs](#admin-apis)

---

## Authentication & Authorization

All endpoints require authentication via JWT token in the Authorization header:

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

## Subscription Management

### 1. Create Subscription

**Endpoint:** `POST /api/subscription/create`

**Auth Required:** All authenticated users (`admin`, `user`, `workspace_admin`, `workspace_user`, `super_admin`)

**Request:**

```json
{
  "planId": "string (required, cannot be empty)",
  "planType": "free | monthly | three_months | six_months | yearly | workspace (required)",
  "totalPrice": 100.0, // number (required, positive)
  "paymentMethod": "bikash | nagad | etc (required)",
  "toolsSubscription": {
    "provider": "openai | anthropic | etc (required)",
    "totalWords": 10000 // number (required, non-negative)
  }
}
```

**Response:**

```json
{
  "statusCode": 201,
  "success": true,
  "message": "Subscription created successfully",
  "data": {
    "userId": "string",
    "plan": "monthly",
    "startDate": "2026-02-06T12:00:00Z",
    "endDate": "2026-03-06T12:00:00Z",
    "tokenLimit": 1000,
    "paymentStatus": "pending | completed | failed",
    "userPaymentStatus": "paid | unpaid",
    "paymentMethod": "bikash | nagad",
    "totalPrice": 100,
    "planId": "string",
    "providers": [
      {
        "name": "string",
        "models": []
      }
    ],
    "toolsSubscription": {
      "provider": "string",
      "totalWords": 10000
    }
  }
}
```

---

### 2. Get User's Active LLM Subscription

**Endpoint:** `GET /api/subscription/llm/active/subscription`

**Auth Required:** All authenticated users

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "LLM Subscription Fetched Successfully",
  "data": {
    // Active LLM subscription details object
    "isActive": true,
    "plan": "monthly",
    "remainingTokens": 5000
    // ...other active plan details
  }
}
```

---

### 3. Get My Subscription

**Endpoint:** `GET /api/subscription/me`

**Auth Required:** All authenticated users

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "User subscription retrieved successfully",
  "data": {
    "userId": "string",
    "plan": "monthly",
    "startDate": "2026-02-06T12:00:00Z",
    "paymentStatus": "completed"
    // ...ISubscription object
  }
}
```

---

### 4. Get My Tools Subscription

**Endpoint:** `GET /api/subscription/me/tools`

**Auth Required:** All authenticated users

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Tools Subscription Fetched Successfully",
  "data": {
    // Tool subscription details (ISubscription or specific tool subset)
    "toolsSubscription": {
      "provider": "string",
      "totalWords": 1000
    }
    // ...
  }
}
```

---

### 5. Get Subscription Summary

**Endpoint:** `GET /api/subscription/summery`

**Auth Required:** All authenticated users

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "User subscription retrieved successfully",
  "data": {
    // Summary object containing subscription and token info
    "subscription": { ... },
    "tokens": {
        "total": 10000,
        "used": 5000,
        "remaining": 5000
    }
  }
}
```

---

### 6. Update Subscription Plan

**Endpoint:** `PATCH /api/subscription/update/:subscriptionId`

**Auth Required:** All authenticated users

**URL Parameters:**

- `subscriptionId` - The ID of the subscription to update

**Request:**

```json
{
  "plan": "monthly | three_months | six_months | yearly | workspace (required)",
  "paymentId": "string (optional)",
  "paymentStatus": "string (optional, used for real-time updates)"
}
```

_Note: The explicit validation middleware is commented out in the routes, but the backend expects these fields._

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Subscription updated successfully",
  "data": {
    // Updated ISubscription object
    "userId": "string",
    "plan": "yearly",
    "paymentStatus": "completed"
    // ...
  }
}
```

---

## Admin APIs

### 7. Get All Subscriptions

**Endpoint:** `GET /api/subscription/all`

**Auth Required:** `admin`, `super_admin`

**Query Parameters:** (Optional filters)

- `page` - Page number
- `limit` - Items per page
- `search` - Search term
- `plan` - Filter by plan type
- `paymentStatus` - Filter by payment status
- `userStatus` - Filter by user status
- `card_type` - Filter by card type
- `card_brand` - Filter by card brand
- `card_issuer` - Filter by card issuer
- `card_category` - Filter by card category

**Request:**

```
GET /api/subscription/all?page=1&limit=10&plan=monthly
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "User subscription retrieved successfully",
  "data": {
    "result": [
      // Array of ISubscription objects
    ],
    "meta": {
      "page": 1,
      "limit": 10,
      "total": 100
    }
  }
}
```

---

### 8. Get User Subscription History

**Endpoint:** `GET /api/subscription/user/:userId/history`

**Auth Required:** `admin`, `super_admin`

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
  "message": "User subscription history fetched successfully",
  "data": [
    {
      // ISubscription object from history
      "plan": "monthly",
      "startDate": "2025-01-01T00:00:00Z",
      "endDate": "2025-02-01T00:00:00Z",
      "paymentStatus": "completed"
      // ...
    }
  ]
}
```

---

## Data Types

### ISubscription

```typescript
{
    userId: string;
    paymentGatewayDetailsId?: string;
    plan: "free" | "monthly" | "three_months" | "six_months" | "yearly" | "workspace";
    startDate: Date;
    endDate?: Date;
    tokenLimit?: number;
    paymentStatus: "pending" | "completed" | "failed";
    userPaymentStatus: "unpaid" | "paid";
    productType?: string;
    paymentId?: string;
    totalPrice?: number;
    planId?: string;
    providers: {
        name: string;
        models: {
            // model details
        }[];
    }[];
    paymentMethod: string;
    toolsSubscription?: {
        provider: string;
        totalWords: number;
    };
}
```
