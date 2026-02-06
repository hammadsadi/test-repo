---

## Base URLs

https://lobster-app-74s7m.ondigitalocean.app

- **V1 API**: `/api/v1`

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
- **Headers**:

```
Authorization: Bearer <access_token>
```

- **Input**: No body required (uses token from header)
- **Output**:

```json
{
  "statusCode": 200,
  "success": true,
  "message": "Logged out successfully",
  "data": {
    "loggedOutAt": "2024-02-06T16:50:00.000Z"
  }
}
```

- **Notes**:
  - Invalidates the current access token
  - Removes refresh token from server
  - Clears authentication cookies if present
  - User must login again to access protected routes

#### 11. Get User by Email

- **Endpoint**: `GET /api/v1/auth/user/:email`
- **Access**: Public
- **Example**: `GET /api/v1/auth/user/john.doe@example.com`
- **Output** (User exists):

```json
{
  "statusCode": 200,
  "success": true,
  "data": {
    "exists": true,
    "user": {
      "email": "john.doe@example.com",
      "name": "John Doe",
      "profilePicture": "https://cdn.example.com/profiles/user123.jpg",
      "isEmailVerified": true
    }
  }
}
```

- **Output** (User not found):

```json
{
  "statusCode": 404,
  "success": false,
  "message": "User not found",
  "data": {
    "exists": false
  }
}
```
