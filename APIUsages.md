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

## Base URLs

https://lobster-app-74s7m.ondigitalocean.app

- **V1 API**: `/api/v1`
- **V2 API**: `/api/v2`

---

## Authentication

All authenticated endpoints require a Bearer token in the Authorization header:

```
Authorization: Bearer <access_token>
```

### Auth Endpoints

#### 1. Register User

- **Endpoint**: `POST /api/v1/auth/register`
- **Access**: Public
- **Input**:

```json
{
  "name": "string (required, min 1 char)",
  "email": "string (required, valid email)",
  "password": "string (required, strong password)"
}
```

- **Output**:

```json
{
  "statusCode": 201,
  "success": true,
  "message": "User registered successfully",
  "data": {
    "userId": "string",
    "email": "string",
    "name": "string"
  }
}
```

#### 2. Login

- **Endpoint**: `POST /api/v1/auth/login`
- **Access**: Public
- **Input**:

```json
{
  "email": "string (required)",
  "password": "string (required)"
}
```

- **Output**:

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Login successful",
  "data": {
    "accessToken": "string",
    "refreshToken": "string",
    "user": {
      "id": "string",
      "email": "string",
      "name": "string",
      "role": "string"
    }
  }
}
```

#### 3. Refresh Token

- **Endpoint**: `POST /api/v1/auth/refresh-token`
- **Access**: Public
- **Input**: Refresh token in cookies or body
- **Output**:

```json
{
  "statusCode": 200,
  "success": true,
  "data": {
    "accessToken": "string"
  }
}
```

#### 4. Change Password

- **Endpoint**: `POST /api/v1/auth/change-password`
- **Access**: Authenticated (USER, ADMIN)
- **Input**:

```json
{
  "oldPassword": "string (required)",
  "newPassword": "string (required, strong)"
}
```

- **Output**:

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Password changed successfully"
}
```

#### 5. Forgot Password

- **Endpoint**: `POST /api/v1/auth/forgot-password`
- **Access**: Public
- **Input**:

```json
{
  "email": "string (required)"
}
```

- **Output**:

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Password reset email sent"
}
```

#### 6. Reset Password

- **Endpoint**: `POST /api/v1/auth/reset-password`
- **Access**: Public
- **Input**:

```json
{
  "token": "string (required)",
  "newPassword": "string (required, strong)"
}
```

- **Output**:

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Password reset successful"
}
```

#### 7. Verify Email

- **Endpoint**: `POST /api/v1/auth/verify-email`
- **Access**: Public
- **Input**:

```json
{
  "email": "string (required)",
  "otp": "string (required, 6 digits)"
}
```

- **Output**:

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Email verified successfully"
}
```

#### 8. Resend Verification

- **Endpoint**: `POST /api/v1/auth/resend-verification`
- **Access**: Public
- **Input**:

```json
{
  "email": "string (required)"
}
```

#### 9. Google OAuth

- **Endpoint**: `POST /api/v1/auth/google`
- **Access**: Public
- **Input**:

```json
{
  "idToken": "string (required, Google ID token)"
}
```

#### 10. Logout

- **Endpoint**: `POST /api/v1/auth/logout`
- **Access**: Authenticated (USER, ADMIN)

#### 11. Get User by Email

- **Endpoint**: `GET /api/v1/auth/user/:email`
- **Access**: Public

---

## Users

### Profile Management

#### 1. Get My Profile

- **Endpoint**: `GET /api/v1/users/me`
- **Access**: Authenticated (All roles)
- **Output**: User profile object

#### 2. Update Profile Image

- **Endpoint**: `PATCH /api/v1/users/update-profile-image`
- **Access**: Authenticated (All roles)
- **Input**: Multipart form-data with `profilePicture` field
- **Allowed**: JPEG, PNG, WebP (max 1 file)

#### 3. Toggle Feedback Emails

- **Endpoint**: `PATCH /api/v1/users/profile/feedback-emails`
- **Access**: Authenticated (All roles)

### Device Management

#### 4. Get My Devices

- **Endpoint**: `GET /api/v1/users/profile/devices`
- **Access**: Authenticated (All roles)

#### 5. Logout Device

- **Endpoint**: `DELETE /api/v1/users/profile/devices/:deviceId`
- **Access**: Authenticated (All roles)

#### 6. Logout All Devices

- **Endpoint**: `POST /api/v1/users/profile/devices/logout-all`
- **Access**: Authenticated (All roles)

### 2FA Management

#### 7. Enable 2FA

- **Endpoint**: `POST /api/v1/users/profile/2fa/enable`
- **Access**: Authenticated (All roles)
- **Output**: QR code and secret

#### 8. Verify 2FA

- **Endpoint**: `POST /api/v1/users/profile/2fa/verify`
- **Access**: Authenticated (All roles)
- **Input**:

```json
{
  "token": "string (6-digit code)"
}
```

#### 9. Disable 2FA

- **Endpoint**: `POST /api/v1/users/profile/2fa/disable`
- **Access**: Authenticated (All roles)

### Login History

#### 10. Get Last Login

- **Endpoint**: `GET /api/v1/users/profile/login-history/last`
- **Access**: Authenticated (All roles)

#### 11. Toggle Login History

- **Endpoint**: `PATCH /api/v1/users/profile/login-history/toggle`
- **Access**: Authenticated (All roles)

### Thread Management

#### 12. Archive All Threads

- **Endpoint**: `POST /api/v1/users/profile/threads/archive-all`
- **Access**: Authenticated (All roles)

#### 13. Get Archived Threads

- **Endpoint**: `GET /api/v1/users/profile/threads/archived`
- **Access**: Authenticated (All roles)

#### 14. Delete All Threads

- **Endpoint**: `DELETE /api/v1/users/profile/threads/delete-all`
- **Access**: Authenticated (All roles)

#### 15. Archive Single Thread

- **Endpoint**: `PATCH /api/v1/users/profile/threads/:threadId/archive`
- **Access**: Authenticated (All roles)

#### 16. Unarchive Thread

- **Endpoint**: `PATCH /api/v1/users/profile/threads/:threadId/unarchive`
- **Access**: Authenticated (All roles)

### Admin Operations

#### 17. Get All Users

- **Endpoint**: `GET /api/v1/users`
- **Access**: ADMIN, SUPER_ADMIN
- **Query Params**: Pagination, filters

#### 18. Get User Statistics

- **Endpoint**: `GET /api/v1/users/statistics`
- **Access**: ADMIN, SUPER_ADMIN

#### 19. Get User by ID

- **Endpoint**: `GET /api/v1/users/:id`
- **Access**: Authenticated (All roles)

#### 20. Update User

- **Endpoint**: `PATCH /api/v1/users/:id`
- **Access**: Authenticated (All roles)

#### 21. Update User by Admin

- **Endpoint**: `PATCH /api/v1/users/update/:id`
- **Access**: ADMIN, SUPER_ADMIN

#### 22. Delete User

- **Endpoint**: `DELETE /api/v1/users/:id`
- **Access**: ADMIN, SUPER_ADMIN

#### 23. Update User Role

- **Endpoint**: `PATCH /api/v1/users/:id/role`
- **Access**: ADMIN, SUPER_ADMIN
- **Input**:

```json
{
  "role": "USER | ADMIN | WORKSPACE_ADMIN | WORKSPACE_USER"
}
```

### Email Change

#### 24. Request Email Change

- **Endpoint**: `POST /api/v1/users/change-email/request`
- **Access**: USER, WORKSPACE_ADMIN, WORKSPACE_USER
- **Input**:

```json
{
  "newEmail": "string"
}
```

#### 25. Verify Email Change

- **Endpoint**: `POST /api/v1/users/change-email/verify`
- **Access**: USER, WORKSPACE_ADMIN, WORKSPACE_USER
- **Input**:

```json
{
  "otp": "string"
}
```

### Account Deletion

#### 26. Request Account Deletion

- **Endpoint**: `POST /api/v1/users/delete/request`
- **Access**: Authenticated (All roles)

#### 27. Confirm Account Deletion

- **Endpoint**: `POST /api/v1/users/delete/confirm`
- **Access**: Authenticated (All roles)

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

## Supported Providers

### LLM Providers

- **openai**: gpt-4, gpt-4-turbo, gpt-3.5-turbo
- **gemini**: gemini-pro, gemini-pro-vision
- **claude**: claude-3-opus, claude-3-sonnet, claude-3-haiku
- **grok**: grok-1, grok-2
- **deepseek**: deepseek-chat, deepseek-coder

### Tool Providers

- **grammarly** (Sapling API)
- **quillbot**
- **gptzero**
- **hixbypass**
