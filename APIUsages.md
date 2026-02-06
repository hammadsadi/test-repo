# LipikaAI Backend API Documentation

## Table of Contents

1. [Authentication](#authentication)
2. [Users](#users)
3. [Workspace](#workspace)
4. [Subscriptions](#subscriptions)
5. [Plans V2](#plans-v2)
6. [Payment](#payment)
7. [Threads](#threads)
8. [Chat V2](#chat-v2)
9. [Token Management](#token-management)
10. [Top-up](#top-up)
11. [Contact](#contact)
12. [Dashboard](#dashboard)
13. [Tools](#tools)
14. [Health](#health)

---

## Users

### Profile Management

#### 1. Get My Profile

- **Endpoint**: `GET /api/v1/users/me`
- **Access**: Authenticated (All roles)
- **Output**:

```json
{
  "statusCode": 200,
  "success": true,
  "data": {
    "user": {
      "id": "65f1a2b3c4d5e6f7g8h9i0j1",
      "name": "John Doe",
      "email": "john.doe@example.com",
      "role": "USER",
      "profilePicture": "https://cdn.example.com/profiles/user123.jpg",
      "isEmailVerified": true,
      "is2FAEnabled": false,
      "feedbackEmailsEnabled": true,
      "loginHistoryEnabled": true,
      "workRole": "developer",
      "phone": "+1234567890",
      "createdAt": "2024-01-15T10:30:00.000Z",
      "lastLoginAt": "2024-02-06T15:20:00.000Z"
    }
  }
}
```

#### 2. Update Profile Image

- **Endpoint**: `PATCH /api/v1/users/update-profile-image`
- **Access**: Authenticated (All roles)
- **Input**: Multipart form-data
  - Field name: `profilePicture`
  - Allowed types: JPEG, PNG, WebP
  - Max files: 1
  - Max size: Configured in server
- **Output**:

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Profile image updated successfully",
  "data": {
    "profilePicture": "https://cdn.example.com/profiles/user123_updated.jpg"
  }
}
```

#### 3. Toggle Feedback Emails

- **Endpoint**: `PATCH /api/v1/users/profile/feedback-emails`
- **Access**: Authenticated (All roles)
- **Output**:

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Feedback emails preference updated",
  "data": {
    "feedbackEmailsEnabled": true
  }
}
```

---

### Device Management

#### 4. Get My Devices

- **Endpoint**: `GET /api/v1/users/profile/devices`
- **Access**: Authenticated (All roles)
- **Output**:

```json
{
  "statusCode": 200,
  "success": true,
  "data": {
    "devices": [
      {
        "deviceId": "65f1a2b3c4d5e6f7g8h9i0k4",
        "deviceName": "Chrome on Windows",
        "deviceType": "desktop",
        "browser": "Chrome 121.0",
        "os": "Windows 11",
        "ipAddress": "192.168.1.100",
        "lastActive": "2024-02-06T16:30:00.000Z",
        "isCurrent": true
      },
      {
        "deviceId": "65f1a2b3c4d5e6f7g8h9i0k5",
        "deviceName": "Safari on iPhone",
        "deviceType": "mobile",
        "browser": "Safari 17.2",
        "os": "iOS 17.2",
        "ipAddress": "192.168.1.101",
        "lastActive": "2024-02-05T14:20:00.000Z",
        "isCurrent": false
      }
    ],
    "total": 2
  }
}
```

#### 5. Logout Device

- **Endpoint**: `DELETE /api/v1/users/profile/devices/:deviceId`
- **Access**: Authenticated (All roles)
- **Output**:

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Device logged out successfully"
}
```

#### 6. Logout All Devices

- **Endpoint**: `POST /api/v1/users/profile/devices/logout-all`
- **Access**: Authenticated (All roles)
- **Output**:

```json
{
  "statusCode": 200,
  "success": true,
  "message": "All devices logged out successfully",
  "data": {
    "devicesLoggedOut": 3
  }
}
```

---

### 2FA Management

#### 7. Enable 2FA

- **Endpoint**: `POST /api/v1/users/profile/2fa/enable`
- **Access**: Authenticated (All roles)
- **Output**:

```json
{
  "statusCode": 200,
  "success": true,
  "message": "2FA setup initiated",
  "data": {
    "secret": "JBSWY3DPEHPK3PXP",
    "qrCode": "data:image/png;base64,iVBORw0KGgoAAAANS...",
    "backupCodes": ["12345678", "87654321", "11223344"]
  }
}
```

#### 8. Verify 2FA

- **Endpoint**: `POST /api/v1/users/profile/2fa/verify`
- **Access**: Authenticated (All roles)
- **Input**:

```json
{
  "token": "123456"
}
```

- **Output**:

```json
{
  "statusCode": 200,
  "success": true,
  "message": "2FA enabled successfully",
  "data": {
    "is2FAEnabled": true
  }
}
```

#### 9. Disable 2FA

- **Endpoint**: `POST /api/v1/users/profile/2fa/disable`
- **Access**: Authenticated (All roles)
- **Input**:

```json
{
  "password": "current_password"
}
```

- **Output**:

```json
{
  "statusCode": 200,
  "success": true,
  "message": "2FA disabled successfully",
  "data": {
    "is2FAEnabled": false
  }
}
```

---

### Login History

#### 10. Get Last Login

- **Endpoint**: `GET /api/v1/users/profile/login-history/last`
- **Access**: Authenticated (All roles)
- **Output**:

```json
{
  "statusCode": 200,
  "success": true,
  "data": {
    "lastLogin": {
      "timestamp": "2024-02-06T15:20:00.000Z",
      "ipAddress": "192.168.1.100",
      "location": "New York, USA",
      "device": "Chrome on Windows",
      "successful": true
    },
    "previousLogin": {
      "timestamp": "2024-02-05T09:15:00.000Z",
      "ipAddress": "192.168.1.101",
      "location": "New York, USA",
      "device": "Safari on iPhone"
    }
  }
}
```

#### 11. Toggle Login History

- **Endpoint**: `PATCH /api/v1/users/profile/login-history/toggle`
- **Access**: Authenticated (All roles)
- **Output**:

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Login history tracking updated",
  "data": {
    "loginHistoryEnabled": true
  }
}
```

---

### Thread Management

#### 12. Archive All Threads

- **Endpoint**: `POST /api/v1/users/profile/threads/archive-all`
- **Access**: Authenticated (All roles)
- **Output**:

```json
{
  "statusCode": 200,
  "success": true,
  "message": "All threads archived successfully",
  "data": {
    "threadsArchived": 15
  }
}
```

#### 13. Get Archived Threads

- **Endpoint**: `GET /api/v1/users/profile/threads/archived`
- **Access**: Authenticated (All roles)
- **Query Params**: `page`, `limit`
- **Output**:

```json
{
  "statusCode": 200,
  "success": true,
  "data": {
    "threads": [
      {
        "id": "65f1a2b3c4d5e6f7g8h9i0k1",
        "title": "Archived Conversation",
        "modelUsed": "gpt-4",
        "messageCount": 10,
        "archivedAt": "2024-02-01T10:00:00.000Z",
        "createdAt": "2024-01-25T14:30:00.000Z"
      }
    ],
    "total": 15,
    "page": 1,
    "limit": 10
  }
}
```

#### 14. Delete All Threads

- **Endpoint**: `DELETE /api/v1/users/profile/threads/delete-all`
- **Access**: Authenticated (All roles)
- **Output**:

```json
{
  "statusCode": 200,
  "success": true,
  "message": "All threads deleted successfully",
  "data": {
    "threadsDeleted": 25
  }
}
```

#### 15. Archive Single Thread

- **Endpoint**: `PATCH /api/v1/users/profile/threads/:threadId/archive`
- **Access**: Authenticated (All roles)
- **Output**:

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Thread archived successfully"
}
```

#### 16. Unarchive Thread

- **Endpoint**: `PATCH /api/v1/users/profile/threads/:threadId/unarchive`
- **Access**: Authenticated (All roles)
- **Output**:

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Thread unarchived successfully"
}
```

---

### Admin Operations

#### 17. Get All Users

- **Endpoint**: `GET /api/v1/users`
- **Access**: ADMIN, SUPER_ADMIN
- **Query Params**:
  - `page` (default: 1)
  - `limit` (default: 10, max: 100)
  - `search` (search by name or email)
  - `role` (filter by role)
  - `isEmailVerified` (true/false)
  - `sortBy` (createdAt, name, email)
  - `sortOrder` (asc, desc)
- **Output**:

```json
{
  "statusCode": 200,
  "success": true,
  "data": {
    "users": [
      {
        "id": "65f1a2b3c4d5e6f7g8h9i0j1",
        "name": "John Doe",
        "email": "john.doe@example.com",
        "role": "USER",
        "isEmailVerified": true,
        "createdAt": "2024-01-15T10:30:00.000Z",
        "lastLoginAt": "2024-02-06T15:20:00.000Z"
      }
    ],
    "pagination": {
      "total": 150,
      "page": 1,
      "limit": 10,
      "totalPages": 15
    }
  }
}
```

#### 18. Get User Statistics

- **Endpoint**: `GET /api/v1/users/statistics`
- **Access**: ADMIN, SUPER_ADMIN
- **Output**:

```json
{
  "statusCode": 200,
  "success": true,
  "data": {
    "totalUsers": 1500,
    "activeUsers": 1200,
    "newUsersThisMonth": 150,
    "usersByRole": {
      "USER": 1300,
      "WORKSPACE_USER": 150,
      "WORKSPACE_ADMIN": 30,
      "ADMIN": 15,
      "SUPER_ADMIN": 5
    },
    "verifiedUsers": 1400,
    "users2FAEnabled": 450
  }
}
```

#### 19. Get User by ID

- **Endpoint**: `GET /api/v1/users/:id`
- **Access**: Authenticated (All roles)
- **Output**:

```json
{
  "statusCode": 200,
  "success": true,
  "data": {
    "user": {
      "id": "65f1a2b3c4d5e6f7g8h9i0j1",
      "name": "John Doe",
      "email": "john.doe@example.com",
      "role": "USER",
      "profilePicture": "https://cdn.example.com/profiles/user123.jpg",
      "isEmailVerified": true,
      "workRole": "developer",
      "createdAt": "2024-01-15T10:30:00.000Z"
    }
  }
}
```

#### 20. Update User

- **Endpoint**: `PATCH /api/v1/users/:id`
- **Access**: Authenticated (All roles - own profile only, unless admin)
- **Input**:

```json
{
  "name": "John Smith",
  "phone": "+1234567891",
  "workRole": "product_manager | developer | designer | marketing | sales | student | researcher | writer | other"
}
```

- **Output**:

```json
{
  "statusCode": 200,
  "success": true,
  "message": "User updated successfully",
  "data": {
    "user": {
      "id": "65f1a2b3c4d5e6f7g8h9i0j1",
      "name": "John Smith",
      "phone": "+1234567891",
      "workRole": "product_manager",
      "updatedAt": "2024-02-06T16:30:00.000Z"
    }
  }
}
```

#### 21. Update User by Admin

- **Endpoint**: `PATCH /api/v1/users/update/:id`
- **Access**: ADMIN, SUPER_ADMIN
- **Input**:

```json
{
  "name": "John Smith",
  "email": "john.smith@example.com",
  "phone": "+1234567891",
  "workRole": "developer"
}
```

- **Output**:

```json
{
  "statusCode": 200,
  "success": true,
  "message": "User updated successfully by admin",
  "data": {
    "user": {
      "id": "65f1a2b3c4d5e6f7g8h9i0j1",
      "name": "John Smith",
      "email": "john.smith@example.com",
      "updatedAt": "2024-02-06T16:30:00.000Z"
    }
  }
}
```

#### 22. Delete User

- **Endpoint**: `DELETE /api/v1/users/:id`
- **Access**: ADMIN, SUPER_ADMIN
- **Output**:

```json
{
  "statusCode": 200,
  "success": true,
  "message": "User deleted successfully"
}
```

#### 23. Update User Role

- **Endpoint**: `PATCH /api/v1/users/:id/role`
- **Access**: ADMIN, SUPER_ADMIN
- **Input**:

```json
{
  "role": "USER | ADMIN | WORKSPACE_ADMIN | WORKSPACE_USER | SUPER_ADMIN"
}
```

- **Output**:

```json
{
  "statusCode": 200,
  "success": true,
  "message": "User role updated successfully",
  "data": {
    "userId": "65f1a2b3c4d5e6f7g8h9i0j1",
    "newRole": "ADMIN"
  }
}
```

#### 24. Get User Devices (Admin)

- **Endpoint**: `GET /api/v1/users/:id/devices`
- **Access**: ADMIN, SUPER_ADMIN
- **Output**: Same as "Get My Devices" but for specified user

#### 25. Logout User Device (Admin)

- **Endpoint**: `DELETE /api/v1/users/:id/devices/:deviceId`
- **Access**: ADMIN, SUPER_ADMIN
- **Output**:

```json
{
  "statusCode": 200,
  "success": true,
  "message": "User device logged out successfully"
}
```

#### 26. Logout All User Devices (Admin)

- **Endpoint**: `POST /api/v1/users/:id/devices/logout-all`
- **Access**: ADMIN, SUPER_ADMIN
- **Output**:

```json
{
  "statusCode": 200,
  "success": true,
  "message": "All user devices logged out successfully",
  "data": {
    "devicesLoggedOut": 5
  }
}
```

---

### Email Change

#### 27. Request Email Change

- **Endpoint**: `POST /api/v1/users/change-email/request`
- **Access**: USER, WORKSPACE_ADMIN, WORKSPACE_USER
- **Input**:

```json
{
  "newEmail": "newemail@example.com"
}
```

- **Output**:

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Verification code sent to new email",
  "data": {
    "expiresIn": "15 minutes"
  }
}
```

#### 28. Verify Email Change

- **Endpoint**: `POST /api/v1/users/change-email/verify`
- **Access**: USER, WORKSPACE_ADMIN, WORKSPACE_USER
- **Input**:

```json
{
  "otp": "123456"
}
```

- **Output**:

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Email changed successfully",
  "data": {
    "newEmail": "newemail@example.com"
  }
}
```

---

### Account Deletion

#### 29. Request Account Deletion

- **Endpoint**: `POST /api/v1/users/delete/request`
- **Access**: Authenticated (All roles)
- **Output**:

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Account deletion request initiated. Confirmation email sent.",
  "data": {
    "confirmationRequired": true,
    "expiresIn": "24 hours"
  }
}
```

#### 30. Confirm Account Deletion

- **Endpoint**: `POST /api/v1/users/delete/confirm`
- **Access**: Authenticated (All roles)
- **Input**:

```json
{
  "confirmationCode": "string",
  "password": "current_password"
}
```

- **Output**:

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Account deletion confirmed. Your account will be deleted within 30 days."
}
```

---

### Password Management (Admin)

#### 31. Get User Password Info

- **Endpoint**: `GET /api/v1/users/:id/password-info`
- **Access**: ADMIN, SUPER_ADMIN
- **Output**:

```json
{
  "statusCode": 200,
  "success": true,
  "data": {
    "hasPassword": true,
    "lastPasswordChange": "2024-01-15T10:30:00.000Z",
    "passwordStrength": "strong"
  }
}
```

#### 32. Reset User Password (Admin)

- **Endpoint**: `POST /api/v1/users/:id/reset-password`
- **Access**: ADMIN, SUPER_ADMIN
- **Input**:

```json
{
  "newPassword": "NewSecurePass123!"
}
```

- **Output**:

```json
{
  "statusCode": 200,
  "success": true,
  "message": "User password reset successfully",
  "data": {
    "passwordResetAt": "2024-02-06T16:30:00.000Z"
  }
}
```

---

## Workspace

### Core Workspace Management

#### 1. Create Workspace

- **Endpoint**: `POST /api/v1/workspace`
- **Access**: ADMIN, SUPER_ADMIN, WORKSPACE_ADMIN
- **Input**:

```json
{
  "name": "Acme Corporation",
  "description": "Marketing team workspace",
  "website": "https://acme.com",
  "maxTeamMembers": 10,
  "billingCycle": "monthly | yearly"
}
```

- **Output**:

```json
{
  "statusCode": 201,
  "success": true,
  "data": {
    "workspaceId": "string",
    "name": "Acme Corporation",
    "status": "pending"
  }
}
```

#### 2. Get My Workspace

- **Endpoint**: `GET /api/v1/workspace/my-workspace`
- **Access**: WORKSPACE_ADMIN, WORKSPACE_USER
- **Output**: Current user's workspace details

#### 3. Get Workspace by ID

- **Endpoint**: `GET /api/v1/workspace/:id`
- **Access**: WORKSPACE_ADMIN, WORKSPACE_USER, ADMIN, SUPER_ADMIN
- **Output**: Complete workspace information

#### 4. Update Workspace

- **Endpoint**: `PUT /api/v1/workspace/:id`
- **Access**: WORKSPACE_ADMIN
- **Input**:

```json
{
  "name": "string",
  "description": "string",
  "settings": {
    "allowUserInvitations": true,
    "requireApprovalForNewUsers": false
  }
}
```

#### 5. Get Workspace Analytics

- **Endpoint**: `GET /api/v1/workspace/:id/analytics`
- **Access**: WORKSPACE_ADMIN, WORKSPACE_USER
- **Query Params**: `period=7d|30d|90d|1y`, `startDate`, `endDate`
- **Output**: Analytics data including usage, token consumption, team activity

---

### Subscription Management

#### 6. Update Workspace Subscription

- **Endpoint**: `PUT /api/v1/workspace/:id/subscription`
- **Access**: WORKSPACE_ADMIN, SUPER_ADMIN
- **Input**:

```json
{
  "maxTeamMembers": 20,
  "billingCycle": "yearly"
}
```

#### 7. Cancel Workspace Subscription

- **Endpoint**: `DELETE /api/v1/workspace/:id/subscription`
- **Access**: WORKSPACE_ADMIN

---

### Team Management

#### 8. Get Team Members

- **Endpoint**: `GET /api/v1/workspace/:id/team`
- **Access**: WORKSPACE_ADMIN, WORKSPACE_USER
- **Output**: List of all team members with roles and permissions

#### 9. Create Team Member

- **Endpoint**: `POST /api/v1/workspace/:id/team/create`
- **Access**: WORKSPACE_ADMIN, ADMIN, SUPER_ADMIN
- **Input**:

```json
{
  "name": "Jane Smith",
  "email": "jane.smith@example.com",
  "phone": "+1234567890",
  "password": "SecurePass123!"
}
```

#### 10. Get Team Member Details

- **Endpoint**: `GET /api/v1/workspace/:id/team/:userId`
- **Access**: WORKSPACE_ADMIN, WORKSPACE_USER

#### 11. Update Member Permissions

- **Endpoint**: `PUT /api/v1/workspace/:id/team/:userId/permissions`
- **Access**: WORKSPACE_ADMIN
- **Input**:

```json
{
  "permissions": {
    "canInviteUsers": true,
    "canManageBilling": false,
    "canViewAnalytics": true,
    "canManageSettings": false
  }
}
```

#### 12. Get Member Usage

- **Endpoint**: `GET /api/v1/workspace/:id/team/:userId/usage`
- **Access**: WORKSPACE_ADMIN, WORKSPACE_USER
- **Output**: Token usage and activity statistics for specific member

#### 13. Get Member Activity

- **Endpoint**: `GET /api/v1/workspace/:id/team/:userId/activity`
- **Access**: WORKSPACE_ADMIN, WORKSPACE_USER
- **Output**: Activity logs and history

#### 14. Deactivate Team Member

- **Endpoint**: `DELETE /api/v1/workspace/:id/team/:userId/deactivate`
- **Access**: WORKSPACE_ADMIN

#### 15. Remove Team Member

- **Endpoint**: `DELETE /api/v1/workspace/:id/team/:userId`
- **Access**: WORKSPACE_ADMIN

---

### Token Usage

#### 16. Get Member Token Usage

- **Endpoint**: `GET /api/v1/workspace/:id/token-usage/members`
- **Access**: WORKSPACE_ADMIN
- **Output**: Token usage breakdown for all team members

#### 17. Get Member Usage by Provider

- **Endpoint**: `GET /api/v1/workspace/:id/token-usage/members/:userId`
- **Access**: WORKSPACE_ADMIN
- **Output**: Provider-specific token usage for a member

---

### Invitation System

#### 18. Invite Users

- **Endpoint**: `POST /api/v1/workspace/:id/invite`
- **Access**: WORKSPACE_ADMIN
- **Input**:

```json
{
  "emails": ["user1@example.com", "user2@example.com"]
}
```

- **Output**:

```json
{
  "statusCode": 200,
  "success": true,
  "data": {
    "invitationsSent": 2,
    "invitations": [...]
  }
}
```

#### 19. Accept Invitation

- **Endpoint**: `POST /api/v1/workspace/invitation/:token/accept`
- **Access**: Authenticated (All roles)

#### 20. Get Pending Invitations

- **Endpoint**: `GET /api/v1/workspace/:id/invitations/pending`
- **Access**: WORKSPACE_ADMIN
- **Output**: List of pending invitations

#### 21. Revoke Invitation

- **Endpoint**: `DELETE /api/v1/workspace/:id/invitations/:invitationId`
- **Access**: WORKSPACE_ADMIN

---

### System Admin Controls

#### 22. Get All Workspaces

- **Endpoint**: `GET /api/v1/workspace`
- **Access**: ADMIN, SUPER_ADMIN
- **Query Params**: `page`, `limit`, `status`, `search`, `sortBy`, `sortOrder`

#### 23. Approve Workspace

- **Endpoint**: `POST /api/v1/workspace/:id/approve`
- **Access**: ADMIN, SUPER_ADMIN

#### 24. Reject Workspace

- **Endpoint**: `POST /api/v1/workspace/:id/reject`
- **Access**: ADMIN, SUPER_ADMIN

#### 25. Update Workspace Status

- **Endpoint**: `PATCH /api/v1/workspace/:id/status`
- **Access**: ADMIN, SUPER_ADMIN
- **Input**:

```json
{
  "status": "active | suspended | pending | cancelled"
}
```

#### 26. Set Team Limit

- **Endpoint**: `PUT /api/v1/workspace/:id/team-limit`
- **Access**: ADMIN, SUPER_ADMIN
- **Input**:

```json
{
  "maxTeamMembers": 50
}
```

#### 27. Get Detailed Stats

- **Endpoint**: `GET /api/v1/workspace/:id/detailed-stats`
- **Access**: ADMIN, SUPER_ADMIN
- **Output**: Comprehensive workspace statistics

#### 28. Delete Workspace

- **Endpoint**: `DELETE /api/v1/workspace/:id`
- **Access**: ADMIN, SUPER_ADMIN

---

## Subscriptions

#### 1. Create Subscription

- **Endpoint**: `POST /api/v1/subscriptions/create`
- **Access**: Authenticated (All roles)
- **Input**:

```json
{
  "planId": "65f1a2b3c4d5e6f7g8h9i0j1",
  "planType": "monthly | three_months | six_months | yearly | workspace",
  "totalPrice": 29.99,
  "paymentMethod": "sslcommerz",
  "toolsSubscription": {
    "provider": "grammarly",
    "totalWords": 50000
  }
}
```

- **Output**:

```json
{
  "statusCode": 201,
  "success": true,
  "message": "Subscription created successfully",
  "data": {
    "subscriptionId": "string",
    "paymentUrl": "string (SSL Commerz payment URL)"
  }
}
```

#### 2. Get Active LLM Subscription

- **Endpoint**: `GET /api/v1/subscriptions/llm/active/subscription`
- **Access**: Authenticated (All roles)
- **Output**:

```json
{
  "statusCode": 200,
  "success": true,
  "data": {
    "subscription": {
      "id": "string",
      "planName": "string",
      "status": "active",
      "providers": [
        {
          "name": "openai",
          "models": [
            {
              "name": "gpt-4",
              "tokensRemaining": 100000
            }
          ]
        }
      ]
    }
  }
}
```

#### 3. Get My Subscription

- **Endpoint**: `GET /api/v1/subscriptions/me`
- **Access**: Authenticated (All roles)
- **Output**: Complete subscription details with all providers and models

#### 4. Get My Tools Subscription

- **Endpoint**: `GET /api/v1/subscriptions/me/tools`
- **Access**: Authenticated (All roles)
- **Output**: Tools subscription details (Grammarly, QuillBot, etc.)

#### 5. Get Subscription Summary

- **Endpoint**: `GET /api/v1/subscriptions/summery`
- **Access**: Authenticated (All roles)
- **Output**: Combined subscription and token usage summary

#### 6. Get All Subscriptions

- **Endpoint**: `GET /api/v1/subscriptions/all`
- **Access**: ADMIN, SUPER_ADMIN
- **Output**: List of all subscriptions in the system

#### 7. Update Subscription

- **Endpoint**: `PATCH /api/v1/subscriptions/update/:subscriptionId`
- **Access**: Authenticated (All roles)
- **Input**:

```json
{
  "plan": "monthly | three_months | six_months | yearly",
  "paymentId": "string (optional)"
}
```

#### 8. Get User Subscription History

- **Endpoint**: `GET /api/v1/subscriptions/user/:userId/history`
- **Access**: ADMIN, SUPER_ADMIN
- **Output**: Historical subscription data for a specific user

---

## Plans V2

#### 1. Create Plan

- **Endpoint**: `POST /api/v2/plan/create`
- **Access**: ADMIN, SUPER_ADMIN
- **Input**:

```json
{
  "name": "string",
  "subHeading": "string",
  "planType": "individual | team",
  "price": "number",
  "providers": [
    {
      "name": "string",
      "totalWords": "number",
      "models": [
        {
          "name": "string",
          "overallTokens": "number",
          "tokensPerUserPerMonth": "number",
          "withCache": "boolean",
          "providerPackage": "string",
          "isTool": "boolean"
        }
      ]
    }
  ],
  "maxTeamMembers": "number (for team plans)"
}
```

#### 2. Get All Plans

- **Endpoint**: `GET /api/v2/plan/`
- **Access**: Public

#### 3. Get All Tools Plans

- **Endpoint**: `GET /api/v2/plan/tools`
- **Access**: Public

#### 4. Get Plans by Provider/Package

- **Endpoint**: `GET /api/v2/plan/filter?provider=openai&package=Thinking`
- **Access**: Public

#### 5. Get Plans with Tool Models

- **Endpoint**: `GET /api/v2/plan/tool-models`
- **Access**: Public

#### 6. Get Individual Plans

- **Endpoint**: `GET /api/v2/plan/individual`
- **Access**: Public

#### 7. Get Team Plans

- **Endpoint**: `GET /api/v2/plan/team`
- **Access**: Public

#### 8. Get Plans by Type

- **Endpoint**: `GET /api/v2/plan/type?type=individual`
- **Access**: Public

#### 9. Get Single Plan

- **Endpoint**: `GET /api/v2/plan/:id`
- **Access**: Public

#### 10. Update Plan

- **Endpoint**: `PATCH /api/v2/plan/:id`
- **Access**: ADMIN, SUPER_ADMIN

#### 11. Delete Plan

- **Endpoint**: `DELETE /api/v2/plan/:id`
- **Access**: ADMIN, SUPER_ADMIN

---

## Payment

#### 1. Validate Payment

- **Endpoint**: `POST /api/v1/payment/validate`
- **Access**: Public

#### 2. Payment Success Callback

- **Endpoint**: `POST /api/v1/payment/success`
- **Access**: Public (SSL Commerz callback)

#### 3. Payment Failed Callback

- **Endpoint**: `POST /api/v1/payment/fail`
- **Access**: Public (SSL Commerz callback)

#### 4. Payment Cancel Callback

- **Endpoint**: `POST /api/v1/payment/cancel`
- **Access**: Public (SSL Commerz callback)

#### 5. Payment IPN

- **Endpoint**: `POST /api/v1/payment/ipn`
- **Access**: Public (SSL Commerz IPN)

#### 6. Get User Payment History

- **Endpoint**: `GET /api/v1/payment/user/:userId/history`
- **Access**: Authenticated (All roles)

#### 7. Get Payment Summary

- **Endpoint**: `GET /api/v1/payment/summery`
- **Access**: ADMIN, SUPER_ADMIN

---

## Threads

#### 1. Create Thread

- **Endpoint**: `POST /api/v1/threads/`
- **Access**: Authenticated (All roles)
- **Input**:

```json
{
  "title": "My AI Conversation",
  "modelUsed": "gpt-4"
}
```

- **Output**:

```json
{
  "statusCode": 201,
  "success": true,
  "message": "Thread created successfully",
  "data": {
    "threadId": "string",
    "title": "My AI Conversation",
    "modelUsed": "gpt-4",
    "createdAt": "ISO 8601 timestamp"
  }
}
```

#### 2. Get Thread by ID

- **Endpoint**: `GET /api/v1/threads/:threadId`
- **Access**: Authenticated (All roles)
- **Output**: Thread details with all messages

#### 3. Get User Threads

- **Endpoint**: `GET /api/v1/threads/`
- **Access**: Authenticated (All roles)
- **Output**: List of all user's threads with message counts

#### 4. Update Thread

- **Endpoint**: `PATCH /api/v1/threads/:id`
- **Access**: Authenticated (All roles)
- **Input**:

```json
{
  "title": "Updated Thread Title"
}
```

---

## Chat V2

#### 1. Create Chat

- **Endpoint**: `POST /api/v2/chat/`
- **Access**: Authenticated (All roles)
- **Input**: Multipart form-data
  - `files`: Up to 5 files (max 20MB each)
  - Other chat parameters in body

#### 2. Get Chat History

- **Endpoint**: `GET /api/v2/chat/history`
- **Access**: Authenticated (All roles)

#### 3. Update Chat

- **Endpoint**: `PATCH /api/v2/chat/:chatId`
- **Access**: Authenticated (All roles)

---

## Token Management

#### 1. Check LLM Token Availability

- **Endpoint**: `POST /api/v1/token/check-llm`
- **Access**: Authenticated (All roles)
- **Input**:

```json
{
  "provider": "openai | gemini | grok | claude | deepseek",
  "model": "gpt-4",
  "estimatedTokens": 1500,
  "imageGenerated": false
}
```

- **Output**:

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Sufficient tokens available",
  "data": {
    "available": true,
    "tokensAvailable": 98500,
    "tokensRequired": 1500
  }
}
```

#### 2. Deduct LLM Tokens

- **Endpoint**: `POST /api/v1/token/deduct-llm`
- **Access**: Authenticated (All roles)
- **Input**:

```json
{
  "provider": "openai | gemini | grok | claude | deepseek",
  "model": "gpt-4",
  "textTokensUsed": 1500,
  "imageGenerated": false
}
```

- **Output**:

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Tokens deducted successfully",
  "data": {
    "tokensDeducted": 1500,
    "tokensRemaining": 97000
  }
}
```

---

## Top-up

#### 1. Get My Top-ups

- **Endpoint**: `GET /api/v1/topup/`
- **Access**: Authenticated (All roles)

---

## Contact

#### 1. Create Contact

- **Endpoint**: `POST /api/v1/contact/create`
- **Access**: Public
- **Input**:

```json
{
  "name": "John Doe",
  "email": "john.doe@example.com",
  "subject": "Product Inquiry",
  "inquiryType": "sales | support | general",
  "message": "I would like to know more about your enterprise plans."
}
```

- **Output**:

```json
{
  "statusCode": 201,
  "success": true,
  "message": "Contact request submitted successfully",
  "data": {
    "contactId": "string",
    "status": "pending"
  }
}
```

#### 2. Get All Contacts

- **Endpoint**: `GET /api/v1/contact/`
- **Access**: ADMIN, SUPER_ADMIN
- **Query Params**: `page`, `limit`, `status`
- **Output**: Paginated list of all contact submissions

#### 3. Get Single Contact

- **Endpoint**: `GET /api/v1/contact/:id`
- **Access**: Public
- **Output**: Complete contact details

#### 4. Delete Contact

- **Endpoint**: `DELETE /api/v1/contact/:id`
- **Access**: ADMIN, SUPER_ADMIN

---

## Dashboard

#### 1. Get Dashboard Overview

- **Endpoint**: `GET /api/v1/dashboard/`
- **Access**: ADMIN, SUPER_ADMIN
- **Output**: Statistics, metrics, user counts, revenue data

---

## Tools

### GPTZero

#### 1. Initiate Scan

- **Endpoint**: `POST /api/v1/gptzero/`
- **Access**: USER, ADMIN
- **Input**: Multipart form-data
  - `files`: Up to 50 files (max 15MB total)
  - Text content in body

#### 2. Get Scan History

- **Endpoint**: `GET /api/v1/gptzero/history`
- **Access**: USER, ADMIN

#### 3. Get GPTZero Threads

- **Endpoint**: `GET /api/v1/gptzero/threads`
- **Access**: USER, ADMIN

### Grammarly (Sapling API)

#### 1. Grammar Check

- **Endpoint**: `POST /api/v1/grammarly/grammar-check`
- **Access**: USER, ADMIN

#### 2. Spellcheck

- **Endpoint**: `POST /api/v1/grammarly/spellcheck`
- **Access**: USER, ADMIN

#### 3. Autocomplete

- **Endpoint**: `POST /api/v1/grammarly/autocomplete`
- **Access**: USER, ADMIN

#### 4. Rephrase

- **Endpoint**: `POST /api/v1/grammarly/rephrase`
- **Access**: USER, ADMIN

#### 5. Tone Detection

- **Endpoint**: `POST /api/v1/grammarly/tone`
- **Access**: USER, ADMIN

#### 6. Sentiment Analysis

- **Endpoint**: `POST /api/v1/grammarly/sentiment`
- **Access**: USER, ADMIN

#### 7. Profanity Check

- **Endpoint**: `POST /api/v1/grammarly/profanity`
- **Access**: USER, ADMIN

#### 8. AI Detector

- **Endpoint**: `POST /api/v1/grammarly/ai-detector`
- **Access**: USER, ADMIN

#### 9. Get Statistics

- **Endpoint**: `POST /api/v1/grammarly/statistics`
- **Access**: USER, ADMIN

#### 10. Get Quality Score

- **Endpoint**: `POST /api/v1/grammarly/quality`
- **Access**: USER, ADMIN

#### 11. Get Analysis History

- **Endpoint**: `GET /api/v1/grammarly/history`
- **Access**: USER, ADMIN

#### 12. Get Threads

- **Endpoint**: `GET /api/v1/grammarly/threads`
- **Access**: USER, ADMIN

### HIX Bypass

#### 1. Initiate Task

- **Endpoint**: `POST /api/v1/hixbypass/`
- **Access**: Authenticated (All roles)
- **Input**: Multipart form-data
  - `files`: Up to 50 files (max 15MB total)

#### 2. Get Threads

- **Endpoint**: `GET /api/v1/hixbypass/threads`
- **Access**: Authenticated (All roles)

#### 3. Get Task History

- **Endpoint**: `GET /api/v1/hixbypass/history`
- **Access**: Authenticated (All roles)

#### 4. Get Usage

- **Endpoint**: `GET /api/v1/hixbypass/usage`
- **Access**: Authenticated (All roles)

### QuillBot

#### 1. Paraphrase

- **Endpoint**: `POST /api/v1/quillbot/rephrase/paraphrase`
- **Access**: USER, ADMIN

#### 2. Compare Modes

- **Endpoint**: `POST /api/v1/quillbot/rephrase/compare-modes`
- **Access**: USER, ADMIN

#### 3. Grammar Check

- **Endpoint**: `POST /api/v1/quillbot/grammar-check`
- **Access**: USER, ADMIN

#### 4. Summarize

- **Endpoint**: `POST /api/v1/quillbot/summarize`
- **Access**: USER, ADMIN

#### 5. Complete Text

- **Endpoint**: `POST /api/v1/quillbot/complete`
- **Access**: USER, ADMIN

#### 6. Detect Tone

- **Endpoint**: `POST /api/v1/quillbot/tone`
- **Access**: USER, ADMIN

#### 7. Analyze Sentiment

- **Endpoint**: `POST /api/v1/quillbot/sentiment`
- **Access**: USER, ADMIN

#### 8. Get Statistics

- **Endpoint**: `POST /api/v1/quillbot/statistics`
- **Access**: USER, ADMIN

#### 9. Get Quality Score

- **Endpoint**: `POST /api/v1/quillbot/quality-score`
- **Access**: USER, ADMIN

#### 10. Detect AI

- **Endpoint**: `POST /api/v1/quillbot/detect-ai`
- **Access**: USER, ADMIN

#### 11. Upload PDF

- **Endpoint**: `POST /api/v1/quillbot/upload-pdf`
- **Access**: Authenticated (All roles)
- **Input**: PDF file (max 50MB)

#### 12. Upload DOCX

- **Endpoint**: `POST /api/v1/quillbot/upload-docx`
- **Access**: Authenticated (All roles)
- **Input**: DOCX file (max 50MB)

#### 13. Get History

- **Endpoint**: `GET /api/v1/quillbot/history`
- **Access**: USER, ADMIN

#### 14. Get Threads

- **Endpoint**: `GET /api/v1/quillbot/threads`
- **Access**: USER, ADMIN

---

## Health

#### 1. Health Check

- **Endpoint**: `GET /api/v1/health`
- **Access**: Public
- **Output**:

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Server is running",
  "data": {
    "status": "success",
    "timestamp": "ISO 8601 string",
    "uptime": "number (seconds)",
    "memoryUsage": "object"
  }
}
```

---

## User Roles

- **USER**: Regular individual user
- **ADMIN**: System administrator
- **SUPER_ADMIN**: Super administrator with full access
- **WORKSPACE_ADMIN**: Workspace administrator
- **WORKSPACE_USER**: Workspace team member

---

## Common Response Format

All API responses follow this structure:

```json
{
  "statusCode": "number",
  "success": "boolean",
  "message": "string",
  "data": "any | null"
}
```

## Error Response Format

```json
{
  "statusCode": "number",
  "success": false,
  "message": "string",
  "errorMessages": [
    {
      "path": "string",
      "message": "string"
    }
  ]
}
```

---

## Notes

1. **Password Requirements**: Passwords must meet strength requirements (validated by `validatePasswordStrength`)
2. **File Uploads**: Different endpoints have different file size and type restrictions
3. **Rate Limiting**: Some endpoints have rate limiting (currently commented out in code)
4. **Pagination**: List endpoints typically support pagination via query parameters
5. **SSL Commerz**: Payment endpoints integrate with SSL Commerz payment gateway

---

### Tool Providers

- **grammarly** (Sapling API)
- **quillbot**
- **gptzero**
- **hixbypass**
