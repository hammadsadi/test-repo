# Plan V2 Module API Documentation

## Base URLs

https://lobster-app-74s7m.ondigitalocean.app

- **V2 API**: `/api/v2`

---

This document provides a comprehensive list of all APIs in the Plan V2 module with their input/output schemas.

## Table of Contents

- [Authentication & Authorization](#authentication--authorization)
- [Plan Management APIs](#plan-management-apis)
- [Public Plan APIs](#public-plan-apis)

---

## Authentication & Authorization

Protected endpoints require authentication via JWT token in the Authorization header:

```
Authorization: Bearer <access_token>
```

### User Roles

- `admin` - System admin
- `super_admin` - Super admin with full access

---

## Plan Management APIs

### 1. Create Plan V2

**Endpoint:** `POST /api/v2/plan/create`

**Auth Required:** `admin`, `super_admin`

**Request:**

```json
{
  "name": "Pro Plan (required)",
  "subHeading": "Best for professionals (required)",
  "planType": "individual | team (optional, default: individual)",
  "price": 29.99, // number (required)
  "providers": [
    {
      "name": "openai",
      "totalWords": 100000,
      "tools": [
        {
          "wordsForUser": 5000,
          "pricePerUser": 0
        }
      ],
      "models": [
        {
          "name": "gpt-4",
          "overallTokens": 100000,
          "tokensPerUserPerMonth": 50000,
          "withCache": false,
          "providerPackage": "standard"
        }
      ]
    }
  ],
  "isTools": false, // boolean (optional)
  "isPopular": true, // boolean (optional)
  "isActive": true, // boolean (optional)
  "maxTeamMembers": 5 // number (optional, for team plans)
}
```

**Response:**

```json
{
  "statusCode": 201,
  "success": true,
  "message": "Plan V2 created successfully",
  "data": {
    "_id": "string",
    "name": "Pro Plan",
    "subHeading": "Best for professionals",
    "planType": "individual",
    "price": 29.99,
    "providers": [
      // ...provider details
    ],
    "isActive": true,
    "createdAt": "2026-02-06T12:00:00Z",
    "updatedAt": "2026-02-06T12:00:00Z"
  }
}
```

---

### 2. Update Plan V2

**Endpoint:** `PATCH /api/v2/plan/:id`

**Auth Required:** `admin`, `super_admin`

**URL Parameters:**

- `id` - Plan ID

**Request:**

```json
{
  "name": "Updated Plan Name (optional)",
  "subHeading": "Updated subheading (optional)",
  "planType": "individual | team (optional)",
  "price": 39.99, // (optional)
  "isTools": false, // (optional)
  "isActive": true, // (optional)
  "isPopular": false, // (optional)
  "maxTeamMembers": 10, // (optional)
  "providers": [
    // ... optional updated provider list
  ]
}
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Plan updated successfully",
  "data": {
    // Updated plan object
  }
}
```

---

### 3. Delete Plan V2

**Endpoint:** `DELETE /api/v2/plan/:id`

**Auth Required:** `admin`, `super_admin`

**URL Parameters:**

- `id` - Plan ID

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Plan deleted successfully",
  "data": {
    // Deleted plan object
  }
}
```

---

## Public Plan APIs

### 4. Get All Plans V2

**Endpoint:** `GET /api/v2/plan/`

**Auth Required:** None (Public)

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Plans fetched successfully",
  "data": [
    // Array of TPlanV2 objects
  ]
}
```

---

### 5. Get All Tools Plans V2

**Endpoint:** `GET /api/v2/plan/tools`

**Auth Required:** None (Public)

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Plans fetched successfully",
  "data": {
    // Tools plan object
  }
}
```

---

### 6. Get Plans by Provider & Package

**Endpoint:** `GET /api/v2/plan/filter`

**Auth Required:** None (Public)

**Query Parameters:**

- `provider` - Provider name (e.g., `openai`)
- `package` - Package type (e.g., `Thinking`)

**Request:**

```
GET /api/v2/plan/filter?provider=openai&package=Thinking
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Plans fetched successfully",
  "data": [
    // Filtered array of plans
  ]
}
```

---

### 7. Get Plans with Tool Models

**Endpoint:** `GET /api/v2/plan/tool-models`

**Auth Required:** None (Public)

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Plans fetched successfully",
  "data": [
    // Array of plans containing tool-specific models
  ]
}
```

---

### 8. Get Individual Plans

**Endpoint:** `GET /api/v2/plan/individual`

**Auth Required:** None (Public)

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Plans fetched successfully",
  "data": [
    // Array of individual plans
  ]
}
```

---

### 9. Get Team Plans

**Endpoint:** `GET /api/v2/plan/team`

**Auth Required:** None (Public)

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Plans fetched successfully",
  "data": [
    // Array of team plans
  ]
}
```

---

### 10. Get Plans by Type

**Endpoint:** `GET /api/v2/plan/type`

**Auth Required:** None (Public)

**Query Parameters:**

- `type` - `individual` or `team`

**Request:**

```
GET /api/v2/plan/type?type=individual
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Plans fetched successfully",
  "data": [
    // Array of plans
  ]
}
```

---

### 11. Get Single Plan by ID

**Endpoint:** `GET /api/v2/plan/:id`

**Auth Required:** None (Public)

**URL Parameters:**

- `id` - Plan ID

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Plan fetched successfully",
  "data": {
    // Single plan object
  }
}
```

---

### 12. Get Single Tools Plan by ID

**Endpoint:** `GET /api/v2/plan/tools/:id`

**Auth Required:** None (Public)

**URL Parameters:**

- `id` - Plan ID

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Plan fetched successfully",
  "data": {
    // Single plan object
  }
}
```

---

## Data Types

### TPlanV2

```typescript
{
    _id?: string;
    name: string;
    subHeading: string;
    planType?: "individual" | "team";
    isTools?: boolean;
    price?: number;
    providers: IProviderModelsV2[];
    isPopular?: boolean;
    isActive?: boolean;
    maxTeamMembers?: number;
    createdAt?: Date;
    updatedAt?: Date;
}
```

### IProviderModelsV2

```typescript
{
    name: string;
    totalWords: number;
    tools?: ITool[];
    models?: IModelDetailsV2[];
}
```

### IModelDetailsV2

```typescript
{
    name: string;
    overallTokens?: number;
    tokensPerUserPerMonth?: number;
    minutesPerUserPerMonth?: number;
    withCache: boolean;
    notes?: string;
    providerPackage?: string;
    isTool?: boolean;
    toolList?: string[];
}
```

### ITool

```typescript
{
  wordsForUser: number;
  pricePerUser: number;
}
```
