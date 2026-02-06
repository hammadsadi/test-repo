# QuillBot Module API Documentation

## Base URLs

https://lobster-app-74s7m.ondigitalocean.app

- **V1 API**: `/api/v1`

---

This document provides a comprehensive list of all APIs in the QuillBot module with their input/output schemas.

## Table of Contents

- [Authentication & Authorization](#authentication--authorization)
- [Core Task APIs](#core-task-apis)
- [Rephrasing APIs](#rephrasing-apis)
- [Grammar Check APIs](#grammar-check-apis)
- [Summarizer & Completion APIs](#summarizer--completion-apis)
- [Analysis APIs](#analysis-apis)
- [File Upload & Utilities](#file-upload--utilities)

---

## Authentication & Authorization

All endpoints (except `/health`) require authentication via JWT token in the Authorization header:

```
Authorization: Bearer <access_token>
```

### User Roles

- `admin` - System admin
- `user` - Regular user

---

## Core Task APIs

### 1. Initiate Standard Task

**Endpoint:** `POST /api/tools/quillbot/`

**Description:** Initiate a standard QuillBot task (general purpose).

**Request:**

```json
{
  "input": "Text to process",
  "mode": "standard | fluency | formal | simple | creative | expand | shorten",
  "language": "en-us | en-gb | en-ca | en-au | de | fr",
  "threadId": "optional_string"
}
```

**Response:**

```json
{
  "success": true,
  "message": "QuillBot task initiated successfully",
  "data": {
    "taskId": "string",
    "output": "Processed text"
    // ...other task fields
  }
}
```

### 2. Get Task History

**Endpoint:** `GET /api/tools/quillbot/history`

**Query Parameters:** `threadId`

### 3. Get User Threads

**Endpoint:** `GET /api/tools/quillbot/threads`

### 4. Delete Thread

**Endpoint:** `DELETE /api/tools/quillbot/threads/delete/:threadId`

---

## Rephrasing APIs

### 5. Paraphrase

**Endpoint:** `POST /api/tools/quillbot/rephrase/paraphrase`

**Description:** Paraphrase text with multiple modes and tone options.

**Request:**

```json
{
  "text": "Text to rephrase",
  "mapping": "paraphrase",
  "tone": "optional_tone_string",
  "numResults": 1,
  "lang": "en",
  "threadId": "optional_string"
}
```

**Response:**

```json
{
  "success": true,
  "message": "Request accepted",
  "data": { "status": "processing", "note": "Check Socket.IO" }
}
```

### 6. Compare Modes

**Endpoint:** `POST /api/tools/quillbot/rephrase/compare-modes`

**Description:** Compare paraphrasing results across different modes simultaneously.

**Request:** Same as Paraphrase.

### 7. Get Paraphrase History

**Endpoint:** `GET /api/tools/quillbot/rephrase/paraphrase`

### 8. Delete Paraphrase Entry

**Endpoint:** `DELETE /api/tools/quillbot/rephrase/delete/:threadId`

---

## Grammar Check APIs

### 9. Check Grammar

**Endpoint:** `POST /api/tools/quillbot/grammar-check`

**Request:**

```json
{
  "text": "Text to check",
  "lang": "en",
  "variety": "US",
  "threadId": "optional_string",
  "sessionId": "optional_string",
  "autoApply": false,
  "ignoreEditTypes": []
}
```

### 10. Accept Edit

**Endpoint:** `POST /api/tools/quillbot/grammar-check/accept-edit/:editId`

### 11. Reject Edit

**Endpoint:** `POST /api/tools/quillbot/grammar-check/reject-edit/:editId`

---

## Summarizer & Completion APIs

### 12. Summarize

**Endpoint:** `POST /api/tools/quillbot/summarize`

**Request:**

```json
{
  "text": "Long text to summarize...",
  "type": "summarize | shorten",
  "threadId": "optional_string"
}
```

### 13. Complete Text (Co-writer)

**Endpoint:** `POST /api/tools/quillbot/complete`

**Request:**

```json
{
  "query": "The future of AI is...",
  "threadId": "optional_string",
  "sessionId": "optional_string"
}
```

### 14. Accept Completion

**Endpoint:** `POST /api/tools/quillbot/complete/accept-completion/:completionId`

### 15. Reject Completion

**Endpoint:** `POST /api/tools/quillbot/complete/reject-completion/:completionId`

---

## Analysis APIs

### 16. Detect Tone

**Endpoint:** `POST /api/tools/quillbot/tone`

**Request:** `{"text": "...", "threadId": "..."}`

### 17. Analyze Sentiment

**Endpoint:** `POST /api/tools/quillbot/sentiment`

**Request:** `{"text": "...", "threadId": "..."}`

### 18. Analyze Statistics

**Endpoint:** `POST /api/tools/quillbot/statistics`

**Request:** `{"text": "...", "threadId": "..."}`

### 19. Quality Score

**Endpoint:** `POST /api/tools/quillbot/quality-score`

**Request:** `{"text": "...", "threadId": "..."}`

### 20. Detect AI Content

**Endpoint:** `POST /api/tools/quillbot/detect-ai`

**Request:** `{"text": "...", "threadId": "..."}`

---

## File Upload & Utilities

### 21. Upload PDF

**Endpoint:** `POST /api/tools/quillbot/upload-pdf`

**Content-Type:** `multipart/form-data`
**Body:** `file` (PDF, max 50MB)

### 22. Upload DOCX

**Endpoint:** `POST /api/tools/quillbot/upload-docx`

**Content-Type:** `multipart/form-data`
**Body:** `file` (DOCX, max 50MB)

### 23. Chunk Text

**Endpoint:** `POST /api/tools/quillbot/chunk-text`

**Request:**

```json
{
  "text": "Long text...",
  "max_length": 1000
}
```

### 24. Chunk HTML

**Endpoint:** `POST /api/tools/quillbot/chunk-html`

**Request:**

```json
{
  "html": "<div>Long html...</div>",
  "max_length": 1000
}
```
