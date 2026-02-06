# Token Module API Documentation

## Base URLs

https://lobster-app-74s7m.ondigitalocean.app

- **V1 API**: `/api/v1`

---

This document provides a comprehensive list of all APIs in the Token module with their input/output schemas.

## Table of Contents

- [Authentication & Authorization](#authentication--authorization)
- [Token Management APIs](#token-management-apis)

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

## Token Management APIs

### 1. Check LLM Token Availability

**Endpoint:** `POST /api/token/check-llm`

**Auth Required:** All authenticated users

**Description:** Validates whether the authenticated user has enough tokens available to perform an LLM operation. Checks Pro plan first, then falls back to Free plan.

**Request:**

```json
{
  "provider": "openai | gemini | grok | claude | deepseek (required)",
  "model": "gpt-4 (required)",
  "estimatedTokens": 150, // number (optional, default: 0)
  "imageGenerated": false // boolean (default: false, adds 1290 tokens if true)
}
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Token availability checked successfully",
  "data": {
    "hasAvailability": true,
    "remaining": 8565
  }
}
```

**Error Response (403):**

```json
{
  "statusCode": 403,
  "success": false,
  "message": "Token limit exceeded"
}
```

---

### 2. Deduct LLM Tokens

**Endpoint:** `POST /api/token/deduct-llm`

**Auth Required:** All authenticated users

**Description:** Deducts the specified number of tokens from the authenticated user's account after an LLM operation has been completed.

**Request:**

```json
{
  "provider": "openai | gemini | grok | claude | deepseek (required)",
  "model": "gpt-4 (required)",
  "textTokensUsed": 245, // number (optional, default: 0)
  "imageGenerated": false // boolean (default: false, adds 1290 tokens if true)
}
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Tokens deducted successfully",
  "data": {
    "plan": "pro | free",
    "tokenLimit": 10000,
    "remainingTokens": 8565,
    "totalUsedTokens": 1435
  }
}
```

**Error Response (403):**

```json
{
  "statusCode": 403,
  "success": false,
  "message": "Token limit exceeded"
}
```

---

## Data Types

### TokenRequest Logic

- **Providers Supported:** `openai`, `gemini`, `grok`, `claude`, `deepseek`
- **Image Token Cost:** 1290 tokens per image

### ITokenStatus

```typescript
{
    isActive: boolean;
    startDate?: Date;
    endDate?: Date;
}
```
