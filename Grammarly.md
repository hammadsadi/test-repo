# Grammarly Module API Documentation

## Base URLs

https://lobster-app-74s7m.ondigitalocean.app

- **V1 API**: `/api/v1`

---

This document provides a comprehensive list of all APIs in the Grammarly module with their input/output schemas.

## Table of Contents

- [Authentication & Authorization](#authentication--authorization)
- [Core Features](#core-features)
- [Extra Features](#extra-features)
- [Dictionary Management](#dictionary-management)
- [Edits Management](#edits-management)
- [Semantic Search](#semantic-search)
- [History & Stats](#history--stats)

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

## Core Features

### 1. Grammar Check

**Endpoint:** `POST /api/tools/grammarly/grammar-check`

**Description:** Check text for grammar and style issues.

**Request:**

```json
{
  "text": "The quick brown fox jumps over the lazy dog.",
  "threadId": "optional_string",
  "opts": {
    "lang": "en-US"
  }
}
```

**Response:**

```json
{
  "success": true,
  "message": "Grammar check complete",
  "data": {
    "edits": [
      {
        "sentence_start": 0,
        "sentence_end": 44,
        "start": 0,
        "end": 3,
        "replacement": "The",
        "error_type": "Case",
        "general_error_type": "Grammar"
      }
    ]
  }
}
```

### 2. Spellcheck

**Endpoint:** `POST /api/tools/grammarly/spellcheck`

**Description:** Run spellcheck only (no grammar edits).

**Request:**

```json
{
  "text": "Words with speling errors.",
  "threadId": "optional_string"
}
```

**Response:**

```json
{
  "success": true,
  "message": "Spellcheck complete",
  "data": { "edits": [ ... ] }
}
```

### 3. Autocomplete

**Endpoint:** `POST /api/tools/grammarly/autocomplete`

**Description:** Get text continuation suggestions.

**Request:**

```json
{
  "text": "The future of AI is",
  "session_id": "optional_string",
  "threadId": "optional_string"
}
```

**Response:**

```json
{
  "success": true,
  "message": "Autocomplete complete",
  "data": {
    "suggestions": [{ "suggestion": " bright.", "confidence": 0.9 }]
  }
}
```

### 4. Rephrase

**Endpoint:** `POST /api/tools/grammarly/rephrase`

**Description:** Generate alternative phrasings.

**Request:**

```json
{
  "text": "I want to get a refund.",
  "options": {
    "mapping": "paraphrase | informal_to_formal",
    "tone_mapping": "friendly"
  },
  "threadId": "optional_string"
}
```

**Response:**

```json
{
  "success": true,
  "message": "Rephrase complete",
  "data": {
    "suggestions": [
      { "replacement": "I would like to request a refund.", "confidence": 0.95 }
    ]
  }
}
```

### 5. Tone Detection

**Endpoint:** `POST /api/tools/grammarly/tone`

**Description:** Detect the tone of the text.

**Request:**

```json
{
  "text": "This is absolutely unacceptable!",
  "threadId": "optional_string"
}
```

**Response:**

```json
{
  "success": true,
  "message": "Tone analysis complete",
  "data": {
    "classification": "angry",
    "confidence": 0.98
  }
}
```

### 6. Sentiment Analysis

**Endpoint:** `POST /api/tools/grammarly/sentiment`

**Description:** Analyze the sentiment (positive/negative) of the text.

**Request:**

```json
{
  "text": "I love this product!",
  "threadId": "optional_string"
}
```

**Response:**

```json
{
  "success": true,
  "message": "Sentiment analysis complete",
  "data": {
    "classification": "positive",
    "confidence": 0.99
  }
}
```

### 7. Profanity Detection

**Endpoint:** `POST /api/tools/grammarly/profanity`

**Description:** Flag offensive language.

**Request:**

```json
{
  "text": "Some text to check.",
  "threadId": "optional_string"
}
```

**Response:**

```json
{
  "success": true,
  "message": "Profanity scan complete",
  "data": {
    "detected": false,
    "confidence": 0.0
  }
}
```

### 8. AI Detector

**Endpoint:** `POST /api/tools/grammarly/ai-detector`

**Description:** Detect AI-generated content.

**Request:**

```json
{
  "text": "Text to analyze.",
  "opts": { "sent_scores": true },
  "threadId": "optional_string"
}
```

**Response:**

```json
{
  "success": true,
  "message": "AI detection complete",
  "data": {
    "score": 0.1, // 0 = Human, 1 = AI
    "sentence_scores": [ ... ]
  }
}
```

---

## Extra Features

### 9. Statistics

**Endpoint:** `POST /api/tools/grammarly/statistics`

**Description:** Get text readability statistics.

**Request:**

```json
{
  "text": "Text to analyze.",
  "lang": "en",
  "threadId": "optional_string"
}
```

**Response:**

```json
{
  "success": true,
  "message": "Text statistics complete",
  "data": { "readability": 80, "word_count": 3 }
}
```

### 10. Quality Score

**Endpoint:** `POST /api/tools/grammarly/quality`

**Description:** Get a quality score (1-5).

**Request:**

```json
{
  "text": "Text to score.",
  "threadId": "optional_string"
}
```

**Response:**

```json
{
  "success": true,
  "message": "Quality score complete",
  "data": { "score": 4.5 }
}
```

### 11. Similar Terms

**Endpoint:** `POST /api/tools/grammarly/similar`

**Description:** Fetch similar terms (thesaurus).

**Request:**

```json
{
  "query": "happy",
  "threadId": "optional_string"
}
```

**Response:**

```json
{
  "success": true,
  "message": "Similar terms fetched",
  "data": { "terms": ["joyful", "content", "glad"] }
}
```

---

## Dictionary Management

### 12. List Dictionary

**Endpoint:** `GET /api/tools/grammarly/dictionary`

**Request:**

```
GET /api/tools/grammarly/dictionary?threadId=...
```

### 13. Add Dictionary Entry

**Endpoint:** `POST /api/tools/grammarly/dictionary`

**Request:**

```json
{
  "entry": "LipikaAI",
  "case_sensitive": true,
  "threadId": "optional_string"
}
```

### 14. Delete Dictionary Entry

**Endpoint:** `DELETE /api/tools/grammarly/dictionary/:entryId`

---

## Edits Management

### 15. Accept Edit

**Endpoint:** `POST /api/tools/grammarly/edits/accept`

**Request:**

```json
{
  "edit_id": "string",
  "session_id": "string",
  "threadId": "optional_string"
}
```

### 16. Reject Edit

**Endpoint:** `POST /api/tools/grammarly/edits/reject`

**Request:**

```json
{
  "edit_id": "string",
  "session_id": "string",
  "threadId": "optional_string"
}
```

---

## Semantic Search

### 17. Search Semantic Bank

**Endpoint:** `POST /api/tools/grammarly/semsearch`

**Request:**

```json
{
  "query": "string",
  "part": "both",
  "threadId": "optional_string"
}
```

### 18. Create Semantic Entry

**Endpoint:** `POST /api/tools/grammarly/semsearch/create`

**Request:**

```json
{
  "team_id": "string",
  "entry": {
    "title": "Topic",
    "prompt": "Question",
    "plain_text": "Answer"
  },
  "threadId": "optional_string"
}
```

### 19. Delete Semantic Entry

**Endpoint:** `DELETE /api/tools/grammarly/semsearch/:entry_id`

---

## History & Stats

### 20. Get Analysis History

**Endpoint:** `GET /api/tools/grammarly/history`

**Query Parameters:**

- `threadId`, `analysisType`, `limit`, `page`

### 21. Get Threads

**Endpoint:** `GET /api/tools/grammarly/threads`

### 22. Get User Statistics

**Endpoint:** `GET /api/tools/grammarly/statistics/user`

### 23. Update Thread Title

**Endpoint:** `PATCH /api/tools/grammarly/threads/update-title/:threadId`

**Request:**

```json
{
  "newTitle": "New Title"
}
```

### 24. Delete Thread

**Endpoint:** `DELETE /api/tools/grammarly/threads/delete/:threadId`
