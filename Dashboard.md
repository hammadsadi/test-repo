# Dashboard Module API Documentation

## Base URLs

https://lobster-app-74s7m.ondigitalocean.app

- **V1 API**: `/api/v1`

---

This document provides a comprehensive list of all APIs in the Dashboard module with their input/output schemas.

## Table of Contents

- [Authentication & Authorization](#authentication--authorization)
- [Dashboard APIs](#dashboard-apis)

---

## Authentication & Authorization

All endpoints require authentication via JWT token in the Authorization header:

```
Authorization: Bearer <access_token>
```

### User Roles

- `admin` - System admin
- `super_admin` - Super admin with full access

---

## Dashboard APIs

### 1. Get Dashboard Overview Data

**Endpoint:** `GET /api/dashboard/`

**Auth Required:** `admin`, `super_admin`

**Description:** Retrieves aggregated high-level data for the admin dashboard, including total users, revenue, token usage, and growth metrics compared to the previous week.

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Dashboard Data Fetched Successfully",
  "data": {
    "totalUsers": 1250,
    "totalRevenue": 45000,
    "totalTokensUsed": 15000000,
    "userGrowth": "12.50", // percentage string
    "revenueGrowth": "8.33", // percentage string
    "tokenGrowth": "15.00", // percentage string
    "aiPlatformUsage": [
      {
        "provider": "openai",
        "totalTokens": 8000000,
        "percentage": "53.33"
      },
      {
        "provider": "gemini",
        "totalTokens": 7000000,
        "percentage": "46.67"
      }
    ],
    "toolsPlatformUsage": [
      {
        "provider": "quillbot",
        "usedWords": 50000,
        "percentage": "60.00"
      },
      {
        "provider": "grammarly",
        "usedWords": 33333,
        "percentage": "40.00"
      }
    ]
  }
}
```

---

## Data Types

### AIPlatformUsageItem

```typescript
{
  provider: string;
  totalTokens: number;
  percentage: string;
}
```

### ToolsPlatformUsageItem

```typescript
{
  provider: string;
  usedWords: number;
  percentage: string;
}
```
