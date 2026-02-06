# User Module API Documentation

## Base URLs

https://lobster-app-74s7m.ondigitalocean.app

- **V1 API**: `/api/v1`

---

This document provides a comprehensive list of all APIs in the User module with their input/output schemas.

## Table of Contents

- [Authentication & Authorization](#authentication--authorization)
- [Profile Management APIs](#profile-management-apis)
- [Device Management APIs](#device-management-apis)
- [Two-Factor Authentication (2FA) APIs](#two-factor-authentication-2fa-apis)
- [Login History APIs](#login-history-apis)
- [Thread/Chat Management APIs](#threadchat-management-apis)
- [User CRUD APIs](#user-crud-apis)
- [Account Management APIs](#account-management-apis)
- [Admin APIs](#admin-apis)

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

## Profile Management APIs

### 1. Get Current User Profile

**Endpoint:** `GET /api/users/me`

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
  "message": "User info fetched successfully",
  "data": {
    "_id": "string",
    "name": "string",
    "email": "string",
    "phone": "string (optional)",
    "workRole": "Design | Management | Marketing | Product | Research | Other (optional)",
    "lipikaUserId": "string",
    "role": "user | workspace_user | workspace_admin | admin | super_admin",
    "isEmailVerified": true,
    "oauthProvider": "local | google (optional)",
    "status": "pending | approved | suspended",
    "profilePicture": "string (optional)",
    "subscription": {
      "plan": "free | monthly | three_months | six_months | yearly | workspace",
      "startDate": "Date",
      "endDate": "Date (optional)",
      "tokenLimit": "number",
      "tokensUsed": "number",
      "isActive": true
    },
    "loginHistoryEnabled": "boolean (optional)",
    "feedbackEmailsEnabled": "boolean (optional)",
    "twoFactor": {
      "enabled": "boolean"
    },
    "workspaceId": "string (optional)",
    "workspaceRole": "admin | user (optional)"
  }
}
```

---

### 2. Toggle Feedback Emails

**Endpoint:** `PATCH /api/users/profile/feedback-emails`

**Auth Required:** All authenticated users

**Request:**

```json
No request body required (toggles current state)
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Feedback emails toggled successfully",
  "data": {
    "feedbackEmailsEnabled": true
  }
}
```

---

### 3. Update Profile Image

**Endpoint:** `PATCH /api/users/update-profile-image`

**Auth Required:** All authenticated users

**Request:**

```
Content-Type: multipart/form-data

Field: profilePicture (file)
Allowed types: image/jpeg, image/png, image/webp
Max files: 1
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Profile image updated successfully",
  "data": {
    "_id": "string",
    "name": "string",
    "email": "string",
    "profilePicture": "https://cdn.example.com/path/to/image.jpg"
  }
}
```

---

## Device Management APIs

### 4. Get My Devices

**Endpoint:** `GET /api/users/profile/devices`

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
  "data": [
    {
      "_id": "string",
      "ipAddress": "192.168.1.1",
      "lastLogin": "2026-02-06T12:00:00Z",
      "deviceName": "Chrome on Windows",
      "platform": "Windows",
      "browser": "Chrome",
      "location": "New York, USA",
      "isCurrent": true
    }
  ]
}
```

---

### 5. Logout from Specific Device

**Endpoint:** `DELETE /api/users/profile/devices/:deviceId`

**Auth Required:** All authenticated users

**URL Parameters:**

- `deviceId` - Device ID to logout from

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Device logged out"
}
```

---

### 6. Logout from All Devices

**Endpoint:** `POST /api/users/profile/devices/logout-all`

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
  "message": "All devices logged out"
}
```

---

## Two-Factor Authentication (2FA) APIs

### 7. Enable 2FA (Generate Secret)

**Endpoint:** `POST /api/users/profile/2fa/enable`

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
  "data": {
    "qrCode": "data:image/png;base64,...",
    "manualKey": "JBSWY3DPEHPK3PXP"
  }
}
```

---

### 8. Verify 2FA Setup

**Endpoint:** `POST /api/users/profile/2fa/verify`

**Auth Required:** All authenticated users

**Request:**

```json
{
  "token": "123456"
}
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "2FA enabled successfully"
}
```

---

### 9. Disable 2FA

**Endpoint:** `POST /api/users/profile/2fa/disable`

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
  "message": "2FA disabled successfully"
}
```

---

## Login History APIs

### 10. Get Last Login History

**Endpoint:** `GET /api/users/profile/login-history/last`

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
  "data": {
    "lastLogin": "2026-02-06T12:00:00Z",
    "ipAddress": "192.168.1.1",
    "location": "New York, USA",
    "deviceName": "Chrome on Windows"
  }
}
```

---

### 11. Toggle Login History Tracking

**Endpoint:** `PATCH /api/users/profile/login-history/toggle`

**Auth Required:** All authenticated users

**Request:**

```json
No request body required (toggles current state)
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Login history tracking toggled",
  "data": {
    "loginHistoryEnabled": true
  }
}
```

---

## Thread/Chat Management APIs

### 12. Archive All Threads

**Endpoint:** `POST /api/users/profile/threads/archive-all`

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
  "message": "All threads archived",
  "data": {
    "archivedCount": 5
  }
}
```

---

### 13. Get Archived Threads

**Endpoint:** `GET /api/users/profile/threads/archived`

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
  "data": [
    {
      "_id": "string",
      "userId": "string",
      "title": "string",
      "isArchived": true,
      "archivedAt": "2026-02-06T12:00:00Z",
      "createdAt": "2026-02-05T10:00:00Z"
    }
  ]
}
```

---

### 14. Delete All Threads

**Endpoint:** `DELETE /api/users/profile/threads/delete-all`

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
  "message": "All threads deleted",
  "data": {
    "deletedCount": 10
  }
}
```

---

### 15. Archive Single Thread

**Endpoint:** `PATCH /api/users/profile/threads/:threadId/archive`

**Auth Required:** All authenticated users

**URL Parameters:**

- `threadId` - Thread ID to archive

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Thread archived",
  "data": {
    "_id": "string",
    "userId": "string",
    "title": "string",
    "isArchived": true,
    "archivedAt": "2026-02-06T12:00:00Z"
  }
}
```

---

### 16. Unarchive Single Thread

**Endpoint:** `PATCH /api/users/profile/threads/:threadId/unarchive`

**Auth Required:** All authenticated users

**URL Parameters:**

- `threadId` - Thread ID to unarchive

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Thread unarchived",
  "data": {
    "_id": "string",
    "userId": "string",
    "title": "string",
    "isArchived": false,
    "archivedAt": null
  }
}
```

---

## User CRUD APIs

### 17. Get User by ID

**Endpoint:** `GET /api/users/:id`

**Auth Required:** All authenticated users

**URL Parameters:**

- `id` - User ID

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "User fetched successfully",
  "data": {
    "_id": "string",
    "name": "string",
    "email": "string",
    "phone": "string (optional)",
    "workRole": "Design | Management | Marketing | Product | Research | Other",
    "role": "user | workspace_user | workspace_admin | admin | super_admin",
    "profilePicture": "string (optional)",
    "subscription": {
      "plan": "free | monthly | three_months | six_months | yearly | workspace",
      "tokenLimit": "number",
      "tokensUsed": "number",
      "isActive": true
    }
  }
}
```

---

### 18. Update User (Self)

**Endpoint:** `PATCH /api/users/:id`

**Auth Required:** All authenticated users (can only update own profile)

**URL Parameters:**

- `id` - User ID (must match authenticated user's ID)

**Request:**

```json
{
  "name": "John Doe (optional, min 2, max 50 chars)",
  "phone": "string (optional)",
  "email": "user@example.com (optional, valid email)",
  "workRole": "Design | Management | Marketing | Product | Research | Other (optional)",
  "password": "string (optional, min 6 chars, must meet strength requirements)"
}
```

**Validation Rules:**

- Password must contain:
  - At least 6 characters
  - At least one uppercase letter
  - At least one lowercase letter
  - At least one number
  - At least one special character

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "User updated successfully",
  "data": {
    "_id": "string",
    "name": "John Doe",
    "email": "user@example.com",
    "phone": "string",
    "workRole": "Design"
  }
}
```

---

## Account Management APIs

### 19. Request Email Change

**Endpoint:** `POST /api/users/change-email/request`

**Auth Required:** `user`, `workspace_admin`, `workspace_user`

**Request:**

```json
{
  "newEmail": "newemail@example.com"
}
```

**Validation:**

- `newEmail` must be a valid email format

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Verification code sent to new email address",
  "data": {
    "pendingEmail": "newemail@example.com",
    "otpSent": true
  }
}
```

---

### 20. Verify Email Change

**Endpoint:** `POST /api/users/change-email/verify`

**Auth Required:** `user`, `workspace_admin`, `workspace_user`

**Request:**

```json
{
  "otp": "123456"
}
```

**Validation:**

- `otp` must be exactly 6 digits
- `otp` must contain only numbers

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Email changed successfully",
  "data": {
    "_id": "string",
    "email": "newemail@example.com"
  }
}
```

---

### 21. Request Account Deletion

**Endpoint:** `POST /api/users/delete/request`

**Auth Required:** All authenticated users

**Request:**

```json
{
  "reason": "string (optional)"
}
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Account deletion OTP sent to your email",
  "data": {
    "otpSent": true,
    "expiresIn": "10 minutes"
  }
}
```

---

### 22. Confirm Account Deletion

**Endpoint:** `POST /api/users/delete/confirm`

**Auth Required:** All authenticated users

**Request:**

```json
{
  "otp": "123456"
}
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Account Deleted Successful",
  "data": {
    "deleted": true,
    "deletedAt": "2026-02-06T12:00:00Z"
  }
}
```

---

### 23. Delete Own Account (Immediate)

**Endpoint:** `PATCH /api/users/my-account`

**Auth Required:** `user`, `workspace_admin`, `workspace_user`

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Account deleted successfully",
  "data": {
    "_id": "string",
    "isDeleted": true
  }
}
```

---

## Admin APIs

### 24. Get All Users

**Endpoint:** `GET /api/users/`

**Auth Required:** `admin`, `super_admin`

**Query Parameters:**

- `page` - Page number (optional, default: 1)
- `limit` - Items per page (optional, default: 10)
- `searchTerm` - Search by name or email (optional)
- `role` - Filter by role (optional)
- `status` - Filter by status (optional)

**Request:**

```
GET /api/users?page=1&limit=10&searchTerm=john&role=user&status=approved
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Users fetched successfully",
  "meta": {
    "page": 1,
    "limit": 10,
    "total": 100
  },
  "data": [
    {
      "_id": "string",
      "name": "string",
      "email": "string",
      "role": "user",
      "status": "approved",
      "isEmailVerified": true,
      "subscription": {
        "plan": "free",
        "isActive": true
      }
    }
  ]
}
```

---

### 25. Get User Statistics

**Endpoint:** `GET /api/users/statistics`

**Auth Required:** `admin`, `super_admin`

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "User statistics fetched successfully",
  "data": {
    "totalUsers": 1000,
    "activeUsers": 850,
    "pendingUsers": 50,
    "suspendedUsers": 100,
    "usersByRole": {
      "user": 800,
      "workspace_user": 150,
      "workspace_admin": 40,
      "admin": 9,
      "super_admin": 1
    },
    "usersByPlan": {
      "free": 700,
      "monthly": 200,
      "yearly": 100
    }
  }
}
```

---

### 26. Update User Info (Admin)

**Endpoint:** `PATCH /api/users/update/:id`

**Auth Required:** `admin`, `super_admin`

**URL Parameters:**

- `id` - User ID to update

**Request:**

```json
{
  "name": "John Doe (optional)",
  "phone": "string (optional)",
  "email": "user@example.com (optional)",
  "workRole": "Design | Management | Marketing | Product | Research | Other (optional)",
  "password": "string (optional)"
}
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "User updated successfully",
  "data": {
    "_id": "string",
    "name": "John Doe",
    "email": "user@example.com",
    "phone": "string",
    "workRole": "Design"
  }
}
```

---

### 27. Delete User (Admin)

**Endpoint:** `DELETE /api/users/:id`

**Auth Required:** `admin`, `super_admin`

**URL Parameters:**

- `id` - User ID to delete

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "User deleted successfully",
  "data": {
    "_id": "string",
    "isDeleted": true
  }
}
```

---

### 28. Update User Role (Admin)

**Endpoint:** `PATCH /api/users/:id/role`

**Auth Required:** `admin`, `super_admin`

**URL Parameters:**

- `id` - User ID

**Request:**

```json
{
  "role": "user | workspace_user | workspace_admin | admin | super_admin"
}
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "User role updated successfully",
  "data": {
    "_id": "string",
    "name": "string",
    "email": "string",
    "role": "admin"
  }
}
```

---

### 29. Get User Devices (Admin)

**Endpoint:** `GET /api/users/:id/devices`

**Auth Required:** `admin`, `super_admin`

**URL Parameters:**

- `id` - User ID

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "data": [
    {
      "_id": "string",
      "ipAddress": "192.168.1.1",
      "lastLogin": "2026-02-06T12:00:00Z",
      "deviceName": "Chrome on Windows",
      "platform": "Windows",
      "browser": "Chrome",
      "location": "New York, USA"
    }
  ]
}
```

---

### 30. Logout User Device (Admin)

**Endpoint:** `DELETE /api/users/:id/devices/:deviceId`

**Auth Required:** `admin`, `super_admin`

**URL Parameters:**

- `id` - User ID
- `deviceId` - Device ID to logout

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Device logged out"
}
```

---

### 31. Logout All User Devices (Admin)

**Endpoint:** `POST /api/users/:id/devices/logout-all`

**Auth Required:** `admin`, `super_admin`

**URL Parameters:**

- `id` - User ID

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "All devices logged out"
}
```

---

### 32. Get User Password Info (Admin)

**Endpoint:** `GET /api/users/:id/password-info`

**Auth Required:** `admin`, `super_admin`

**URL Parameters:**

- `id` - User ID

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "data": {
    "hasPassword": true,
    "oauthProvider": "local | google",
    "lastPasswordChange": "2026-01-15T10:00:00Z"
  }
}
```

---

### 33. Reset User Password (Admin)

**Endpoint:** `POST /api/users/:id/reset-password`

**Auth Required:** `admin`, `super_admin`

**URL Parameters:**

- `id` - User ID

**Request:**

```json
{
  "newPassword": "NewSecurePass123!"
}
```

**Validation:**

- Password must be at least 6 characters
- Password must be at most 50 characters
- Password must meet strength requirements:
  - At least one uppercase letter
  - At least one lowercase letter
  - At least one number
  - At least one special character

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "User password reset successfully",
  "data": {
    "passwordReset": true,
    "userId": "string"
  }
}
```

---

## Error Responses

All endpoints may return the following error responses:

### 400 Bad Request

```json
{
  "statusCode": 400,
  "success": false,
  "message": "Validation error message",
  "errorMessages": [
    {
      "path": "field_name",
      "message": "Specific validation error"
    }
  ]
}
```

### 401 Unauthorized

```json
{
  "statusCode": 401,
  "success": false,
  "message": "Unauthorized access"
}
```

### 403 Forbidden

```json
{
  "statusCode": 403,
  "success": false,
  "message": "You don't have permission to access this resource"
}
```

### 404 Not Found

```json
{
  "statusCode": 404,
  "success": false,
  "message": "Resource not found"
}
```

### 500 Internal Server Error

```json
{
  "statusCode": 500,
  "success": false,
  "message": "Internal server error"
}
```

---

## Notes

1. **Authentication**: All endpoints require a valid JWT token in the Authorization header unless specified otherwise.

2. **Base URL**: All endpoints are prefixed with `/api/users` (e.g., `/api/users/me`, `/api/users/profile/devices`).

3. **Date Format**: All dates are returned in ISO 8601 format (e.g., `2026-02-06T12:00:00Z`).

4. **Pagination**: List endpoints support pagination with `page` and `limit` query parameters.

5. **File Uploads**: File upload endpoints use `multipart/form-data` content type.

6. **Password Requirements**:
   - Minimum 6 characters
   - At least one uppercase letter
   - At least one lowercase letter
   - At least one number
   - At least one special character

7. **OTP Format**: All OTPs are 6-digit numeric codes with expiration times.

8. **Role Hierarchy**:
   - `super_admin` > `admin` > `workspace_admin` > `workspace_user` > `user`

9. **Soft Delete**: User deletion is typically a soft delete (sets `isDeleted: true`) rather than permanently removing the record.

10. **Rate Limiting**: Some endpoints may have rate limiting applied (check with backend team for specific limits).
