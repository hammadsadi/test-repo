# TopUp Module API Documentation

## Base URLs

https://lobster-app-74s7m.ondigitalocean.app

- **V1 API**: `/api/v1`

---

This document provides a comprehensive list of all APIs in the TopUp module with their input/output schemas.

## Table of Contents

- [Authentication & Authorization](#authentication--authorization)
- [TopUp Management APIs](#topup-management-apis)

---

## Authentication & Authorization

All endpoints require authentication via JWT token in the Authorization header:

```
Authorization: Bearer <access_token>
```

### User Roles

- `admin` - System admin
- `super_admin` - Super admin with full access
- `user` - Regular user
- `workspace_admin` - Admin of a workspace
- `workspace_user` - User within a workspace

---

## TopUp Management APIs

### 1. Get All My TopUps

**Endpoint:** `GET /api/topup/`

**Auth Required:** All authenticated users

**Description:** Retrieves all TopUp packages associated with the authenticated user. TopUps are essentially scaled-down versions (50%) of Plans.

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 201,
  "success": true,
  "message": "ToUp Retrieved Successfully",
  "data": [
    {
      "_id": "string",
      "userId": "string",
      "planId": "string",
      "name": "Pro Plan TopUp",
      "subHeading": "Additional tokens",
      "price": 15,
      "providers": [
        {
          "name": "openai",
          "totalWords": 50000,
          "models": [
            {
              "name": "gpt-4",
              "overallTokens": 50000,
              "tokensPerUserPerMonth": 25000,
              "withCache": false
            }
          ]
        }
      ],
      "isPopular": false,
      "createdAt": "2026-02-06T12:00:00Z",
      "updatedAt": "2026-02-06T12:00:00Z"
    }
  ]
}
```

---

## Data Types

### TTopUp

```typescript
{
    _id?: string;
    userId: string;
    planId: string;
    name: string;
    subHeading: string;
    isTools?: boolean;
    price?: number;
    providers: IProviderModels[];
    isPopular?: boolean;
    createdAt?: Date;
    updatedAt?: Date;
}
```
