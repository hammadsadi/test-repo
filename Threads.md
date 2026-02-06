# Threads Module API Documentation

## Base URLs

https://lobster-app-74s7m.ondigitalocean.app

- **V1 API**: `/api/v1`

---

This document provides a comprehensive list of all APIs in the Threads module with their input/output schemas.

## Table of Contents

- [Authentication & Authorization](#authentication--authorization)
- [Thread Management APIs](#thread-management-apis)
- [Socket Events](#socket-events)

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

## Thread Management APIs

### 1. Create Thread

**Endpoint:** `POST /api/threads`

**Auth Required:** All authenticated users

**Request:**

```json
{
  "title": "My New Chat (required)",
  "botProvider": "openai | gemini | claude | deepseek | grok | huggingface (required)",
  "modelUsed": "gpt-4 | claude-3-opus | etc (required)"
}
```

**Response:**

```json
{
  "statusCode": 201,
  "success": true,
  "message": "Thread created successfully",
  "data": {
    "_id": "string",
    "userId": "string",
    "title": "My New Chat",
    "botProvider": "openai",
    "modelUsed": "gpt-4",
    "createdAt": "2026-02-06T12:00:00Z",
    "updatedAt": "2026-02-06T12:00:00Z"
  }
}
```

**Socket Emits:** `threadCreated` to user's room

---

### 2. Get Thread by ID

**Endpoint:** `GET /api/threads/:threadId`

**Auth Required:** All authenticated users

**URL Parameters:**

- `threadId` - The ID of the thread

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Thread fetched successfully",
  "data": {
    "_id": "string",
    "userId": "string",
    "title": "My New Chat",
    "botProvider": "openai",
    "modelUsed": "gpt-4",
    "createdAt": "2026-02-06T12:00:00Z",
    "updatedAt": "2026-02-06T12:00:00Z"
  }
}
```

**Socket Emits:** `threadFetched` to user's room

---

### 3. Get User Threads

**Endpoint:** `GET /api/threads`

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
  "message": "Threads fetched successfully",
  "data": [
    {
      "_id": "string",
      "userId": "string",
      "title": "My Chat",
      "botProvider": "openai",
      "modelUsed": "gpt-4",
      "createdAt": "2026-02-06T12:00:00Z",
      "updatedAt": "2026-02-06T12:00:00Z"
    }
  ]
}
```

**Socket Emits:** `userThreadsFetched` to user's room

---

### 4. Update Thread

**Endpoint:** `PATCH /api/threads/:id`

**Auth Required:** All authenticated users

**URL Parameters:**

- `id` - The ID of the thread to update

**Request:**

```json
{
  "title": "Updated Chat Title"
  // ...other fields from IThread if allowed
}
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Thread updated successfully",
  "data": {
    "_id": "string",
    "title": "Updated Chat Title"
    // ...updated thread object
  }
}
```

**Socket Emits:** `threadUpdated` to user's room

---

## Socket Events

The backend emits real-time events to the `/chat` namespace.

**Events:**

- `threadCreated`: Emitted when a new thread is created.
- `threadFetched`: Emitted when a specific thread is retrieved.
- `userThreadsFetched`: Emitted when all user threads are retrieved.
- `threadUpdated`: Emitted when a thread is updated.

**Payload Structure:**

```typescript
{
  threadId: string;
  userId: string;
  title: string;
  botProvider: string;
  modelUsed: string;
  createdAt: Date;
  updatedAt: Date;
}
```

## Data Types

### IThread

```typescript
{
  _id: any;
  userId: any;
  title: string;
  botProvider: string;
  tool?: string;
  modelUsed: string;
  createdAt: Date;
  updatedAt: Date;
}
```
