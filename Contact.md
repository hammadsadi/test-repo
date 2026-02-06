# Contact Module API Documentation

## Base URLs

https://lobster-app-74s7m.ondigitalocean.app

- **V1 API**: `/api/v1`

---

This document provides a comprehensive list of all APIs in the Contact module with their input/output schemas.

## Table of Contents

- [Authentication & Authorization](#authentication--authorization)
- [Contact Management APIs](#contact-management-apis)

---

## Authentication & Authorization

Some endpoints require authentication via JWT token in the Authorization header:

```
Authorization: Bearer <access_token>
```

### User Roles

- `admin` - System admin
- `super_admin` - Super admin with full access

---

## Contact Management APIs

### 1. Create Contact Message

**Endpoint:** `POST /api/contact/create`

**Auth Required:** None (Public)

**Description:** Allows users or visitors to send a contact message or inquiry.

**Request:**

```json
{
  "name": "John Doe (required)",
  "email": "john@example.com (required, valid email)",
  "subject": "Inquiry about pricing (required)",
  "inquiryType": "Sales | Support | General (required)",
  "message": "Hello, I would like to know... (required)"
}
```

**Response:**

```json
{
  "statusCode": 201,
  "success": true,
  "message": "Message sent successfully!",
  "data": {
    "name": "John Doe",
    "email": "john@example.com",
    "subject": "Inquiry about pricing",
    "inquiryType": "Sales",
    "message": "Hello, I would like to know...",
    "_id": "string",
    "createdAt": "2026-02-06T12:00:00Z",
    "updatedAt": "2026-02-06T12:00:00Z"
  }
}
```

---

### 2. Get All Contacts

**Endpoint:** `GET /api/contact/`

**Auth Required:** `admin`, `super_admin`

**Description:** Retrieves a list of all contact messages submitted to the system.

**Query Parameters:**

- `page` - Page number (optional)
- `limit` - Items per page (optional)
- `search` - Search term (optional)

**Request:**

```
GET /api/contact/?page=1&limit=10
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Message retrieved successfully!",
  "data": {
    "result": [
      // Array of IContact objects
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

### 3. Get Single Contact

**Endpoint:** `GET /api/contact/:id`

**Auth Required:** None (Public)

**URL Parameters:**

- `id` - The ID of the contact message

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Message retrieved successfully!",
  "data": {
    "_id": "string",
    "name": "John Doe",
    "email": "john@example.com"
    // ...other IContact fields
  }
}
```

---

### 4. Delete Contact

**Endpoint:** `DELETE /api/contact/:id`

**Auth Required:** `admin`, `super_admin`

**URL Parameters:**

- `id` - The ID of the contact message to delete

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Message Deleted successfully!",
  "data": {
    // Deleted IContact object
  }
}
```

---

## Data Types

### IContact

```typescript
{
    name: string;
    email: string;
    subject: string;
    inquiryType: string;
    message: string;
    _id?: string;
    createdAt?: Date;
    updatedAt?: Date;
}
```
