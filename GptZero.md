# GPTZero Module API Documentation

## Base URLs

https://lobster-app-74s7m.ondigitalocean.app

- **V1 API**: `/api/v1`

---

This document provides a comprehensive list of all APIs in the GPTZero module with their input/output schemas.

## Table of Contents

- [Authentication & Authorization](#authentication--authorization)
- [GPTZero APIs](#gptzero-apis)

---

## Authentication & Authorization

All endpoints require authentication via JWT token in the Authorization header:

```
Authorization: Bearer <access_token>
```

### User Roles

- `admin` - System admin
- `user` - Regular user

---

## GPTZero APIs

### 1. Initiate Scan

**Endpoint:** `POST /api/gptzero/`

**Auth Required:** `user`, `admin`

**Description:** Initiates a text or file scan to detect AI-generated content. Supports plain text or PDF files (max 50 files, 15MB total).

**Request (Text):**

```json
{
  "text": "Text to scan (max 50,000 chars)",
  "threadId": "string (optional)",
  "multilingual": false, // boolean (optional)
  "modelVersion": "string (optional)",
  "apiVersion": "string (optional)"
}
```

**Request (File):**

- Content-Type: `multipart/form-data`
- Body:
  - `files`: File[] (1-50 files, .txt or .pdf)
  - `threadId`: string (optional)

**Response:**

```json
{
  "statusCode": 201,
  "success": true,
  "message": "Scan initiated successfully",
  "data": {
    "scanId": "string",
    "documentId": "string",
    "predictedClass": "human | ai | mixed",
    "classProbabilities": {
      "ai": 0.1,
      "human": 0.9,
      "mixed": 0.0
    },
    "averageGeneratedProb": 0.15,
    "confidenceCategory": "high | medium | low",
    "resultMessage": "Likely written by a human",
    "sentences": [
      {
        "sentence": "This is a sentence.",
        "generated_prob": 0.1,
        "highlight_sentence_for_ai": false
      }
    ]
    // ...other IGptZeroScan fields
  }
}
```

---

### 2. Get Scan History

**Endpoint:** `GET /api/gptzero/history`

**Auth Required:** `user`, `admin`

**Description:** Retrieves the user's history of GPTZero scans.

**Query Parameters:**

- `threadId` - Filter by specific thread ID (optional)

**Request:**

```
GET /api/gptzero/history?threadId=12345
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "History retrieved successfully",
  "data": [
    {
      // IGptZeroScan object
      "scanId": "string",
      "predictedClass": "ai",
      "createdAt": "2026-02-06T12:00:00Z"
    }
  ]
}
```

---

### 3. Get GPTZero Threads

**Endpoint:** `GET /api/gptzero/threads`

**Auth Required:** `user`, `admin`

**Description:** Retrieves all threads associated with GPTZero scans for the user.

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "GPTZero threads retrieved successfully",
  "data": [
    // Array of thread objects
  ]
}
```

---

## Data Types

### IGptZeroScan

```typescript
{
    threadId: any;
    userId: any;
    scanId: string;
    documentId: string;
    inputText?: string;
    predictedClass: 'human' | 'ai' | 'mixed';
    classProbabilities: {
        ai: number;
        human: number;
        mixed: number;
    };
    averageGeneratedProb: number;
    resultMessage: string;
    documentClassification: 'HUMAN_ONLY' | 'MIXED' | 'AI_ONLY';
    confidenceCategory: 'high' | 'medium' | 'low';
    wordCount: number;
    sentences?: {
        generated_prob: number;
        perplexity: number;
        sentence: string;
        highlight_sentence_for_ai: boolean;
    }[];
    createdAt?: Date;
    updatedAt?: Date;
}
```
