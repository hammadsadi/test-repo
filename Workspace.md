# Workspace Module API Documentation

## Base URLs

https://lobster-app-74s7m.ondigitalocean.app

- **V1 API**: `/api/v1`

---

This document provides a comprehensive list of all APIs in the Workspace module with their input/output schemas.

## Table of Contents

- [Authentication & Authorization](#authentication--authorization)
- [Core Workspace Management](#core-workspace-management)
- [Subscription Management](#subscription-management)
- [Team & Member Management](#team--member-management)
- [Token Usage & Analytics](#token-usage--analytics)
- [Invitation System](#invitation-system)
- [System Admin Controls](#system-admin-controls)

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

## Core Workspace Management

### 1. Create Workspace

**Endpoint:** `POST /api/workspace`

**Auth Required:** `admin`, `super_admin`, `workspace_admin`

**Request:**

```json
{
  "name": "Acme Corporation (required, min 2, max 100 chars)",
  "description": "Our company workspace (optional, max 500 chars)",
  "website": "https://example.com (optional, valid URL)",
  "maxTeamMembers": 5,
  "billingCycle": "monthly | yearly (optional, default: monthly)"
}
```

**Response:**

```json
{
  "statusCode": 201,
  "success": true,
  "message": "Workspace created successfully",
  "data": {
    "_id": "string",
    "name": "Acme Corporation",
    "displayName": "Acme Corporation",
    "description": "Our company workspace",
    "website": "https://example.com",
    "adminUserId": "string",
    "status": "pending",
    "subscription": {
      "planType": "starter_workspace",
      "maxUsers": 5,
      "currentUsers": 1,
      "startDate": "2026-02-06T12:00:00Z",
      "endDate": "2027-02-06T12:00:00Z",
      "isActive": true,
      "billingCycle": "monthly",
      "price": 99.99,
      "features": {
        "maxTokensPerUser": 100000,
        "maxSessionsPerUser": 1000,
        "advancedAnalytics": true,
        "prioritySupport": false,
        "customBranding": false,
        "apiAccess": false
      }
    },
    "teamMembers": [],
    "settings": {
      "allowUserInvitations": true,
      "requireApprovalForNewUsers": false,
      "defaultUserPermissions": {
        "canInviteUsers": false,
        "canManageBilling": false,
        "canViewAnalytics": true,
        "canManageSettings": false
      }
    },
    "createdAt": "2026-02-06T12:00:00Z",
    "updatedAt": "2026-02-06T12:00:00Z"
  }
}
```

---

### 2. Get My Workspace

**Endpoint:** `GET /api/workspace/my-workspace`

**Auth Required:** `workspace_admin`, `workspace_user`

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Workspace fetched successfully",
  "data": {
    "_id": "string",
    "name": "Acme Corporation",
    "displayName": "Acme Corporation",
    "description": "Our company workspace",
    "website": "https://example.com",
    "logo": "https://cdn.example.com/logo.png",
    "adminUserId": "string",
    "status": "active",
    "subscription": {
      "planType": "professional_workspace",
      "maxUsers": 20,
      "currentUsers": 15,
      "startDate": "2026-02-06T12:00:00Z",
      "endDate": "2027-02-06T12:00:00Z",
      "isActive": true,
      "billingCycle": "yearly",
      "price": 999.99,
      "features": {
        "maxTokensPerUser": 500000,
        "maxSessionsPerUser": 5000,
        "advancedAnalytics": true,
        "prioritySupport": true,
        "customBranding": true,
        "apiAccess": true
      }
    },
    "teamMembers": [
      {
        "userId": "string",
        "email": "user@example.com",
        "name": "John Doe",
        "role": "workspace_user",
        "invitedBy": "string",
        "invitedAt": "2026-01-15T10:00:00Z",
        "joinedAt": "2026-01-15T11:00:00Z",
        "status": "accepted",
        "permissions": {
          "canInviteUsers": false,
          "canManageBilling": false,
          "canViewAnalytics": true,
          "canManageSettings": false
        },
        "usage": {
          "tokensUsed": 15000,
          "lastActive": "2026-02-06T10:00:00Z",
          "totalSessions": 45
        }
      }
    ],
    "settings": {
      "allowUserInvitations": true,
      "requireApprovalForNewUsers": false,
      "defaultUserPermissions": {
        "canInviteUsers": false,
        "canManageBilling": false,
        "canViewAnalytics": true,
        "canManageSettings": false
      },
      "branding": {
        "customLogo": "https://cdn.example.com/custom-logo.png",
        "customColors": {
          "primary": "#3B82F6",
          "secondary": "#10B981"
        }
      }
    },
    "createdAt": "2026-01-01T00:00:00Z",
    "updatedAt": "2026-02-06T12:00:00Z"
  }
}
```

---

### 3. Get Workspace by ID

**Endpoint:** `GET /api/workspace/:id`

**Auth Required:** `workspace_admin`, `workspace_user`, `admin`, `super_admin`

**URL Parameters:**

- `id` - Workspace ID

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Workspace fetched successfully",
  "data": {
    "_id": "string",
    "name": "Acme Corporation",
    "displayName": "Acme Corporation",
    "description": "Our company workspace",
    "status": "active",
    "subscription": {
      "planType": "professional_workspace",
      "maxUsers": 20,
      "currentUsers": 15,
      "isActive": true
    },
    "teamMembers": [],
    "settings": {}
  }
}
```

---

### 4. Update Workspace

**Endpoint:** `PUT /api/workspace/:id`

**Auth Required:** `workspace_admin`

**URL Parameters:**

- `id` - Workspace ID

**Request:**

```json
{
  "name": "Updated Workspace Name (optional, min 2, max 100 chars)",
  "description": "Updated description (optional, max 500 chars)",
  "displayName": "Display Name (optional, min 2, max 50 chars)",
  "website": "https://newwebsite.com (optional, valid URL)",
  "settings": {
    "allowUserInvitations": true,
    "requireApprovalForNewUsers": false,
    "defaultUserPermissions": {
      "canInviteUsers": false,
      "canManageBilling": false,
      "canViewAnalytics": true,
      "canManageSettings": false
    },
    "branding": {
      "customLogo": "https://cdn.example.com/logo.png",
      "customColors": {
        "primary": "#3B82F6",
        "secondary": "#10B981"
      }
    }
  }
}
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Workspace updated successfully",
  "data": {
    "_id": "string",
    "name": "Updated Workspace Name",
    "displayName": "Display Name",
    "description": "Updated description",
    "website": "https://newwebsite.com",
    "settings": {
      "allowUserInvitations": true,
      "requireApprovalForNewUsers": false,
      "defaultUserPermissions": {
        "canInviteUsers": false,
        "canManageBilling": false,
        "canViewAnalytics": true,
        "canManageSettings": false
      },
      "branding": {
        "customLogo": "https://cdn.example.com/logo.png",
        "customColors": {
          "primary": "#3B82F6",
          "secondary": "#10B981"
        }
      }
    },
    "updatedAt": "2026-02-06T12:00:00Z"
  }
}
```

---

### 5. Get Workspace Analytics

**Endpoint:** `GET /api/workspace/:id/analytics`

**Auth Required:** `workspace_admin`, `workspace_user` (with permissions)

**URL Parameters:**

- `id` - Workspace ID

**Query Parameters:**

- `period` - Time period: `7d`, `30d`, `90d`, `1y` (default: `30d`)
- `startDate` - Custom start date (ISO 8601 format, optional)
- `endDate` - Custom end date (ISO 8601 format, optional)

**Request:**

```
GET /api/workspace/:id/analytics?period=30d
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Analytics fetched successfully",
  "data": {
    "totalTokensUsed": 1500000,
    "totalSessions": 2500,
    "activeUsers": 18,
    "lastActivity": "2026-02-06T11:30:00Z",
    "monthlyUsage": [
      {
        "month": "2026-01",
        "tokensUsed": 800000,
        "sessions": 1200,
        "activeUsers": 15
      },
      {
        "month": "2026-02",
        "tokensUsed": 700000,
        "sessions": 1300,
        "activeUsers": 18
      }
    ],
    "userUsage": [
      {
        "userId": "string",
        "userName": "John Doe",
        "tokensUsed": 150000,
        "sessions": 250,
        "lastActive": "2026-02-06T10:00:00Z",
        "providerUsage": [
          {
            "provider": "chatgpt",
            "tokensUsed": 80000
          },
          {
            "provider": "claude",
            "tokensUsed": 50000
          },
          {
            "provider": "gemini",
            "tokensUsed": 20000
          }
        ]
      }
    ]
  }
}
```

---

## Subscription Management

### 6. Update Workspace Subscription

**Endpoint:** `PUT /api/workspace/:id/subscription`

**Auth Required:** `workspace_admin`, `super_admin`

**URL Parameters:**

- `id` - Workspace ID

**Request:**

```json
{
  "maxTeamMembers": 30,
  "billingCycle": "monthly | yearly (optional)"
}
```

**Validation:**

- `maxTeamMembers` must be between 1 and 50

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Subscription updated successfully",
  "data": {
    "_id": "string",
    "subscription": {
      "planType": "professional_workspace",
      "maxUsers": 30,
      "currentUsers": 15,
      "billingCycle": "yearly",
      "isActive": true,
      "startDate": "2026-02-06T12:00:00Z",
      "endDate": "2027-02-06T12:00:00Z"
    }
  }
}
```

---

### 7. Cancel Workspace Subscription

**Endpoint:** `DELETE /api/workspace/:id/subscription`

**Auth Required:** `workspace_admin`

**URL Parameters:**

- `id` - Workspace ID

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Subscription cancelled successfully",
  "data": {
    "_id": "string",
    "subscription": {
      "isActive": false,
      "endDate": "2026-02-06T12:00:00Z"
    },
    "status": "cancelled"
  }
}
```

---

## Team & Member Management

### 8. Get Workspace Team Members

**Endpoint:** `GET /api/workspace/:id/team`

**Auth Required:** `workspace_admin`, `workspace_user`

**URL Parameters:**

- `id` - Workspace ID

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Team members fetched successfully",
  "data": [
    {
      "userId": "string",
      "email": "user@example.com",
      "name": "John Doe",
      "role": "workspace_user",
      "invitedBy": "string",
      "invitedAt": "2026-01-15T10:00:00Z",
      "joinedAt": "2026-01-15T11:00:00Z",
      "status": "accepted",
      "permissions": {
        "canInviteUsers": false,
        "canManageBilling": false,
        "canViewAnalytics": true,
        "canManageSettings": false
      },
      "usage": {
        "tokensUsed": 15000,
        "lastActive": "2026-02-06T10:00:00Z",
        "totalSessions": 45
      }
    }
  ]
}
```

---

### 9. Create Team Member Directly

**Endpoint:** `POST /api/workspace/:id/team/create`

**Auth Required:** `workspace_admin`, `admin`, `super_admin`

**URL Parameters:**

- `id` - Workspace ID

**Request:**

```json
{
  "name": "Jane Smith (required, min 2, max 100 chars)",
  "email": "jane@example.com (required, valid email)",
  "phone": "+1234567890 (required, min 10 chars)",
  "password": "SecurePass123 (required, min 8 chars)"
}
```

**Validation:**

- Password must contain uppercase, lowercase, and number

**Response:**

```json
{
  "statusCode": 201,
  "success": true,
  "message": "Team member created successfully",
  "data": {
    "userId": "string",
    "email": "jane@example.com",
    "name": "Jane Smith",
    "role": "workspace_user",
    "invitedBy": "string",
    "invitedAt": "2026-02-06T12:00:00Z",
    "joinedAt": "2026-02-06T12:00:00Z",
    "status": "accepted",
    "permissions": {
      "canInviteUsers": false,
      "canManageBilling": false,
      "canViewAnalytics": true,
      "canManageSettings": false
    }
  }
}
```

---

### 10. Get Team Member Details

**Endpoint:** `GET /api/workspace/:id/team/:userId`

**Auth Required:** `workspace_admin`, `workspace_user`

**URL Parameters:**

- `id` - Workspace ID
- `userId` - Team member user ID

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Team member details fetched successfully",
  "data": {
    "userId": "string",
    "email": "user@example.com",
    "name": "John Doe",
    "role": "workspace_user",
    "invitedBy": "string",
    "invitedAt": "2026-01-15T10:00:00Z",
    "joinedAt": "2026-01-15T11:00:00Z",
    "status": "accepted",
    "permissions": {
      "canInviteUsers": false,
      "canManageBilling": false,
      "canViewAnalytics": true,
      "canManageSettings": false
    },
    "usage": {
      "tokensUsed": 15000,
      "lastActive": "2026-02-06T10:00:00Z",
      "totalSessions": 45
    }
  }
}
```

---

### 11. Update Team Member Permissions

**Endpoint:** `PUT /api/workspace/:id/team/:userId/permissions`

**Auth Required:** `workspace_admin`

**URL Parameters:**

- `id` - Workspace ID
- `userId` - Team member user ID

**Request:**

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

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Permissions updated successfully",
  "data": {
    "userId": "string",
    "permissions": {
      "canInviteUsers": true,
      "canManageBilling": false,
      "canViewAnalytics": true,
      "canManageSettings": false
    }
  }
}
```

---

### 12. Get Team Member Usage

**Endpoint:** `GET /api/workspace/:id/team/:userId/usage`

**Auth Required:** `workspace_admin`, `workspace_user`

**URL Parameters:**

- `id` - Workspace ID
- `userId` - Team member user ID

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Usage statistics fetched successfully",
  "data": {
    "userId": "string",
    "userName": "John Doe",
    "tokensUsed": 150000,
    "sessions": 250,
    "lastActive": "2026-02-06T10:00:00Z",
    "providerUsage": [
      {
        "provider": "chatgpt",
        "tokensUsed": 80000
      },
      {
        "provider": "claude",
        "tokensUsed": 50000
      },
      {
        "provider": "gemini",
        "tokensUsed": 20000
      }
    ]
  }
}
```

---

### 13. Get Team Member Activity

**Endpoint:** `GET /api/workspace/:id/team/:userId/activity`

**Auth Required:** `workspace_admin`, `workspace_user`

**URL Parameters:**

- `id` - Workspace ID
- `userId` - Team member user ID

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Activity logs fetched successfully",
  "data": {
    "userId": "string",
    "userName": "John Doe",
    "activities": [
      {
        "timestamp": "2026-02-06T10:00:00Z",
        "action": "chat_created",
        "details": "Created new chat session",
        "tokensUsed": 1500
      },
      {
        "timestamp": "2026-02-06T09:30:00Z",
        "action": "login",
        "details": "User logged in",
        "ipAddress": "192.168.1.1"
      }
    ]
  }
}
```

---

### 14. Deactivate Team Member

**Endpoint:** `DELETE /api/workspace/:id/team/:userId/deactivate`

**Auth Required:** `workspace_admin`

**URL Parameters:**

- `id` - Workspace ID
- `userId` - Team member user ID

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Team member deactivated successfully",
  "data": {
    "userId": "string",
    "status": "deactivated",
    "deactivatedAt": "2026-02-06T12:00:00Z"
  }
}
```

---

### 15. Remove User from Workspace

**Endpoint:** `DELETE /api/workspace/:id/team/:userId`

**Auth Required:** `workspace_admin`

**URL Parameters:**

- `id` - Workspace ID
- `userId` - Team member user ID

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "User removed from workspace successfully",
  "data": {
    "userId": "string",
    "removedAt": "2026-02-06T12:00:00Z"
  }
}
```

---

## Token Usage & Analytics

### 16. Get Workspace Member Token Usage

**Endpoint:** `GET /api/workspace/:id/token-usage/members`

**Auth Required:** `workspace_admin`

**URL Parameters:**

- `id` - Workspace ID

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Token usage fetched successfully",
  "data": [
    {
      "userId": "string",
      "userName": "John Doe",
      "email": "john@example.com",
      "tokensUsed": 150000,
      "sessions": 250,
      "lastActive": "2026-02-06T10:00:00Z",
      "percentageOfTotal": 15.5
    },
    {
      "userId": "string",
      "userName": "Jane Smith",
      "email": "jane@example.com",
      "tokensUsed": 120000,
      "sessions": 200,
      "lastActive": "2026-02-05T18:00:00Z",
      "percentageOfTotal": 12.4
    }
  ]
}
```

---

### 17. Get Member Usage Per Provider

**Endpoint:** `GET /api/workspace/:id/token-usage/members/:userId`

**Auth Required:** `workspace_admin`

**URL Parameters:**

- `id` - Workspace ID
- `userId` - Team member user ID

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Provider usage fetched successfully",
  "data": {
    "userId": "string",
    "userName": "John Doe",
    "totalTokensUsed": 150000,
    "providerBreakdown": [
      {
        "provider": "chatgpt",
        "tokensUsed": 80000,
        "percentage": 53.3,
        "sessions": 120
      },
      {
        "provider": "claude",
        "tokensUsed": 50000,
        "percentage": 33.3,
        "sessions": 80
      },
      {
        "provider": "gemini",
        "tokensUsed": 15000,
        "percentage": 10.0,
        "sessions": 30
      },
      {
        "provider": "grok",
        "tokensUsed": 3000,
        "percentage": 2.0,
        "sessions": 10
      },
      {
        "provider": "deepseek",
        "tokensUsed": 2000,
        "percentage": 1.4,
        "sessions": 10
      }
    ]
  }
}
```

---

## Invitation System

### 18. Invite Users to Workspace

**Endpoint:** `POST /api/workspace/:id/invite`

**Auth Required:** `workspace_admin`

**URL Parameters:**

- `id` - Workspace ID

**Request:**

```json
{
  "emails": ["user1@example.com", "user2@example.com", "user3@example.com"]
}
```

**Validation:**

- At least one email is required
- All emails must be valid email format

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Invitations sent successfully",
  "data": {
    "invitationsSent": 3,
    "invitations": [
      {
        "_id": "string",
        "workspaceId": "string",
        "email": "user1@example.com",
        "invitedBy": "string",
        "role": "workspace_user",
        "token": "unique-token-string",
        "expiresAt": "2026-02-13T12:00:00Z",
        "status": "pending",
        "createdAt": "2026-02-06T12:00:00Z"
      }
    ]
  }
}
```

---

### 19. Accept Invitation

**Endpoint:** `POST /api/workspace/invitation/:token/accept`

**Auth Required:** All authenticated users

**URL Parameters:**

- `token` - Unique invitation token

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Invitation accepted successfully",
  "data": {
    "workspaceId": "string",
    "userId": "string",
    "role": "workspace_user",
    "joinedAt": "2026-02-06T12:00:00Z",
    "permissions": {
      "canInviteUsers": false,
      "canManageBilling": false,
      "canViewAnalytics": true,
      "canManageSettings": false
    }
  }
}
```

---

### 20. Get Pending Invitations

**Endpoint:** `GET /api/workspace/:id/invitations/pending`

**Auth Required:** `workspace_admin`

**URL Parameters:**

- `id` - Workspace ID

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Pending invitations fetched successfully",
  "data": [
    {
      "_id": "string",
      "workspaceId": "string",
      "email": "user@example.com",
      "invitedBy": "string",
      "role": "workspace_user",
      "token": "unique-token-string",
      "expiresAt": "2026-02-13T12:00:00Z",
      "status": "pending",
      "createdAt": "2026-02-06T12:00:00Z"
    }
  ]
}
```

---

### 21. Revoke Invitation

**Endpoint:** `DELETE /api/workspace/:id/invitations/:invitationId`

**Auth Required:** `workspace_admin`

**URL Parameters:**

- `id` - Workspace ID
- `invitationId` - Invitation ID to revoke

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Invitation revoked successfully",
  "data": {
    "_id": "string",
    "status": "expired",
    "revokedAt": "2026-02-06T12:00:00Z"
  }
}
```

---

## System Admin Controls

### 22. Get All Workspaces

**Endpoint:** `GET /api/workspace`

**Auth Required:** `admin`, `super_admin`

**Query Parameters:**

- `page` - Page number (default: 1, must be > 0)
- `limit` - Items per page (default: 10, max: 100)
- `status` - Filter by status: `active`, `suspended`, `pending`, `cancelled`
- `planType` - Filter by plan: `starter_workspace`, `professional_workspace`, `enterprise_workspace`
- `search` - Search term (max 100 chars)
- `sortBy` - Sort field: `name`, `createdAt`, `subscription.planType`, `subscription.price` (default: `createdAt`)
- `sortOrder` - Sort order: `asc`, `desc` (default: `desc`)

**Request:**

```
GET /api/workspace?page=1&limit=10&status=active&sortBy=createdAt&sortOrder=desc
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Workspaces fetched successfully",
  "meta": {
    "page": 1,
    "limit": 10,
    "total": 150,
    "totalPages": 15
  },
  "data": [
    {
      "_id": "string",
      "name": "Acme Corporation",
      "displayName": "Acme Corporation",
      "adminUserId": "string",
      "status": "active",
      "subscription": {
        "planType": "professional_workspace",
        "maxUsers": 20,
        "currentUsers": 15,
        "isActive": true,
        "billingCycle": "yearly",
        "price": 999.99
      },
      "createdAt": "2026-01-01T00:00:00Z",
      "updatedAt": "2026-02-06T12:00:00Z"
    }
  ]
}
```

---

### 23. Approve Workspace

**Endpoint:** `POST /api/workspace/:id/approve`

**Auth Required:** `admin`, `super_admin`

**URL Parameters:**

- `id` - Workspace ID

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Workspace approved successfully",
  "data": {
    "_id": "string",
    "name": "Acme Corporation",
    "status": "active",
    "approvedAt": "2026-02-06T12:00:00Z"
  }
}
```

---

### 24. Reject Workspace

**Endpoint:** `POST /api/workspace/:id/reject`

**Auth Required:** `admin`, `super_admin`

**URL Parameters:**

- `id` - Workspace ID

**Request:**

```json
{
  "reason": "Reason for rejection (optional)"
}
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Workspace rejected successfully",
  "data": {
    "_id": "string",
    "name": "Acme Corporation",
    "status": "suspended",
    "rejectedAt": "2026-02-06T12:00:00Z",
    "rejectionReason": "Reason for rejection"
  }
}
```

---

### 25. Update Workspace Status

**Endpoint:** `PATCH /api/workspace/:id/status`

**Auth Required:** `admin`, `super_admin`

**URL Parameters:**

- `id` - Workspace ID

**Request:**

```json
{
  "status": "active | suspended | pending | cancelled"
}
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Workspace status updated successfully",
  "data": {
    "_id": "string",
    "name": "Acme Corporation",
    "status": "active",
    "updatedAt": "2026-02-06T12:00:00Z"
  }
}
```

---

### 26. Set Custom Team Member Limit

**Endpoint:** `PUT /api/workspace/:id/team-limit`

**Auth Required:** `admin`, `super_admin`

**URL Parameters:**

- `id` - Workspace ID

**Request:**

```json
{
  "customMaxUsers": 50
}
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Team member limit updated successfully",
  "data": {
    "_id": "string",
    "subscription": {
      "maxUsers": 20,
      "customMaxUsers": 50,
      "currentUsers": 15
    }
  }
}
```

---

### 27. Get Detailed Workspace Statistics

**Endpoint:** `GET /api/workspace/:id/detailed-stats`

**Auth Required:** `admin`, `super_admin`

**URL Parameters:**

- `id` - Workspace ID

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Detailed statistics fetched successfully",
  "data": {
    "workspaceId": "string",
    "name": "Acme Corporation",
    "status": "active",
    "subscription": {
      "planType": "professional_workspace",
      "maxUsers": 20,
      "currentUsers": 15,
      "isActive": true,
      "billingCycle": "yearly",
      "price": 999.99,
      "startDate": "2026-01-01T00:00:00Z",
      "endDate": "2027-01-01T00:00:00Z",
      "daysRemaining": 329
    },
    "usage": {
      "totalTokensUsed": 1500000,
      "totalSessions": 2500,
      "activeUsers": 18,
      "averageTokensPerUser": 83333,
      "averageSessionsPerUser": 139
    },
    "teamStatistics": {
      "totalMembers": 20,
      "activeMembers": 18,
      "pendingInvitations": 5,
      "deactivatedMembers": 2
    },
    "financials": {
      "monthlyRevenue": 999.99,
      "yearlyRevenue": 11999.88,
      "nextBillingDate": "2027-01-01T00:00:00Z"
    },
    "activity": {
      "lastActivity": "2026-02-06T11:30:00Z",
      "mostActiveUser": {
        "userId": "string",
        "userName": "John Doe",
        "tokensUsed": 150000
      }
    }
  }
}
```

---

### 28. Delete Workspace

**Endpoint:** `DELETE /api/workspace/:id`

**Auth Required:** `admin`, `super_admin`

**URL Parameters:**

- `id` - Workspace ID

**Request:**

```json
No request body required
```

**Response:**

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Workspace deleted successfully",
  "data": {
    "_id": "string",
    "name": "Acme Corporation",
    "deletedAt": "2026-02-06T12:00:00Z",
    "isDeleted": true
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
  "message": "Workspace not found"
}
```

### 409 Conflict

```json
{
  "statusCode": 409,
  "success": false,
  "message": "Workspace already exists or user limit reached"
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

## Data Types & Enums

### Workspace Status

- `active` - Workspace is active and operational
- `suspended` - Workspace is temporarily suspended
- `pending` - Workspace is awaiting approval
- `cancelled` - Workspace subscription is cancelled

### Invitation Status

- `pending` - Invitation sent but not yet accepted
- `accepted` - Invitation accepted by user
- `declined` - Invitation declined by user
- `expired` - Invitation expired or revoked

### Plan Types

- `starter_workspace` - Starter plan for small teams
- `professional_workspace` - Professional plan for growing teams
- `enterprise_workspace` - Enterprise plan for large organizations

### Billing Cycles

- `monthly` - Monthly billing
- `yearly` - Yearly billing

### AI Providers

- `chatgpt` - OpenAI ChatGPT
- `claude` - Anthropic Claude
- `gemini` - Google Gemini
- `grok` - xAI Grok
- `deepseek` - DeepSeek

---

## Notes

1. **Authentication**: All endpoints require a valid JWT token in the Authorization header.

2. **Base URL**: All endpoints are prefixed with `/api/workspace`.

3. **Date Format**: All dates are in ISO 8601 format (e.g., `2026-02-06T12:00:00Z`).

4. **Pagination**: List endpoints support pagination with `page` and `limit` query parameters.

5. **Workspace Roles**:
   - `workspace_admin` - Full control over workspace
   - `workspace_user` - Limited access based on permissions

6. **Permissions**: Team members can have granular permissions:
   - `canInviteUsers` - Can invite new users to workspace
   - `canManageBilling` - Can manage subscription and billing
   - `canViewAnalytics` - Can view workspace analytics
   - `canManageSettings` - Can modify workspace settings

7. **Invitation Expiry**: Invitations typically expire after 7 days.

8. **Team Member Limits**: Based on subscription plan, can be overridden by admins.

9. **Token Usage**: Tracked per user and per AI provider for detailed analytics.

10. **Soft Delete**: Workspace deletion is typically a soft delete for data recovery purposes.

11. **Analytics Periods**:
    - `7d` - Last 7 days
    - `30d` - Last 30 days
    - `90d` - Last 90 days
    - `1y` - Last year

12. **Custom Branding**: Available for professional and enterprise plans.

13. **Rate Limiting**: Some endpoints may have rate limiting (check with backend team).

14. **Workspace Creation**: Workspaces are typically created automatically when purchasing a Team Plan.

15. **Subscription Management**: Subscription updates are handled via Plan V2 Team Plan purchases.
