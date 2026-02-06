# HIX Bypass Module API Documentation

## Base URLs

https://lobster-app-74s7m.ondigitalocean.app

- **V1 API**: `/api/v1`

---

This document provides a comprehensive list of all APIs in the HIX Bypass module with their input/output schemas.

## Table of Contents

- [Authentication & Authorization](#authentication--authorization)
- [HIX Bypass APIs](#hix-bypass-apis)

---

## Authentication & Authorization

All endpoints require authentication via JWT token in the Authorization header:

```
Authorization: Bearer <access_token>
```

### User Roles

- `admin` - System admin
- `user` - Regular user
- `super_admin`, `workspace_admin`, `workspace_user`

---

## HIX Bypass APIs

### 1. Initiate HIX Bypass Task

**Endpoint:** `POST /api/tools/hixbypass/`

**Description:** Initiates a humanization task for text or file input. Either `input` text or `files` (up to 50, PDF/Text) must be provided.

**Request (Text):**

```json
{
  "input": "Text to humanize...",
  "mode": "Fast | Balanced | Aggressive | Latest (required)",
  "threadId": "optional_string"
}
```

**Request (File):**

- Content-Type: `multipart/form-data`
- Body:
  - `files`: File[] (1-50 files, .txt or .pdf)
  - `mode`: "Fast | Balanced | Aggressive | Latest" (required)
  - `threadId`: string (optional)

**Response:**

```json
{
  "statusCode": 201,
  "success": true,
  "message": "Task initiated successfully",
  "data": {
    "taskId": "task_12345",
    "userId": "string",
    "threadId": "string",
    "status": "pending",
    "mode": "Balanced",
    "createdAt": "2026-02-06T12:00:00Z"
    // ...IHixBypassTask fields
  }
}
```

### 2. Get Task History

**Endpoint:** `GET /api/tools/hixbypass/history`

**Description:** Retrieves task history for the authenticated user.

**Query Parameters:**

- `threadId` - Filter by thread ID (optional)

**Request:**

```
GET /api/tools/hixbypass/history?threadId=...
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Task history retrieved successfully",
  "data": [
    {
      "taskId": "task_12345",
      "input": "...",
      "output": "...",
      "status": "completed"
      // ...IHixBypassTask fields
    }
  ]
}
```

### 3. Get Usage Statistics

**Endpoint:** `GET /api/tools/hixbypass/usage`

**Description:** Retrieves word credit usage statistics.

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Usage retrieved successfully",
  "data": {
    "total_words": 10000,
    "use_words": 500
  }
}
```

### 4. Get HIX Bypass Threads

**Endpoint:** `GET /api/tools/hixbypass/threads`

**Description:** Retrieves all threads associated with HIX Bypass tasks.

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "HixBypass threads retrieved successfully",
  "data": [
    // Array of thread objects
  ]
}
```

---

## Data Types

### IHixBypassTask

```typescript
{
    _id: string;
    taskId: string;
    userId: string;
    threadId: string;
    input?: string;
    inputWords?: number;
    output?: string;
    wordsUsed?: number;
    subtaskStatus?: 'running' | 'completed' | 'failed';
    taskStatus?: string;
    detectionResult?: 'uncertain' | 'ai' | 'human';
    detectionScore?: number;
    mode: 'Fast' | 'Balanced' | 'Aggressive' | 'Latest';
    createdAt: Date;
    updatedAt: Date;
}
```
