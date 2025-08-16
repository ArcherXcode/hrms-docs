# Authentication API

The Authentication API handles user login, logout, and token management for the HRMS system.

## Endpoints Overview

| Method | Endpoint | Description | Access |
|--------|----------|-------------|---------|
| POST | `/auth/signup` | User registration | HR, Admin |
| POST | `/auth/login` | User login | Public |
| POST | `/auth/logout` | User logout | Authenticated |
| POST | `/auth/reset-password` | Reset password with token | Public |

## POST /auth/signup

Register a new user account (HR/Admin only).

### Request

```http
POST /api/auth/signup
Authorization: Bearer <admin-token>
Content-Type: application/json

{
  "email": "jane.smith@company.com",
  "password": "securePassword123",
  "firstName": "Jane",
  "lastName": "Smith",
  "role": "employee",
  "department": "Engineering",
  "position": "Software Developer",
  "managerId": "550e8400-e29b-41d4-a716-446655440001",
  "startDate": "2024-03-01"
}
```

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| email | string | Yes | User's email address |
| password | string | Yes | User's password (min 8 characters) |
| firstName | string | Yes | User's first name |
| lastName | string | Yes | User's last name |
| role | string | Yes | User role (employee, manager, hr, admin) |
| department | string | Yes | Department name |
| position | string | Yes | Job position/title |
| managerId | string | No | Manager's user ID |
| startDate | string | Yes | Employment start date (YYYY-MM-DD) |
| phone | string | No | Phone number |
| address | string | No | Home address |

### Response

**Success (201 Created)**

```json
{
  "success": true,
  "message": "User registered successfully",
  "data": {
    "user": {
      "id": "550e8400-e29b-41d4-a716-446655440002",
      "email": "jane.smith@company.com",
      "firstName": "Jane",
      "lastName": "Smith",
      "role": "employee",
      "department": "Engineering",
      "position": "Software Developer",
      "managerId": "550e8400-e29b-41d4-a716-446655440001",
      "startDate": "2024-03-01",
      "status": "active",
      "createdAt": "2024-02-20T10:00:00Z"
    },
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
  }
}
```

**Error (400 Bad Request)**

```json
{
  "success": false,
  "message": "User registration failed",
  "errors": [
    {
      "field": "email",
      "message": "Email already exists"
    },
    {
      "field": "password",
      "message": "Password must be at least 8 characters"
    }
  ]
}
```

**Error (403 Forbidden)**

```json
{
  "success": false,
  "message": "Insufficient permissions. Only HR and Admin can register new users."
}
```

## POST /auth/login
| POST | `/auth/forgot-password` | Request password reset | Public |
| POST | `/auth/reset-password` | Reset password with token | Public |

## POST /auth/login

Authenticate user and receive JWT token.

### Request

```http
POST /api/auth/login
Content-Type: application/json

{
  "email": "john.doe@company.com",
  "password": "securePassword123"
}
```

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| email | string | Yes | User's email address |
| password | string | Yes | User's password |

### Response

**Success (200 OK)**

```json
{
  "success": true,
  "message": "Login successful",
  "data": {
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "user": {
      "id": "550e8400-e29b-41d4-a716-446655440000",
      "email": "john.doe@company.com",
      "firstName": "John",
      "lastName": "Doe",
      "role": "employee",
      "organizationId": "550e8400-e29b-41d4-a716-446655440001",
      "departmentId": "550e8400-e29b-41d4-a716-446655440002",
      "permissions": ["read:profile", "write:profile"]
    },
    "expiresIn": "24h"
  }
}
```

**Error (401 Unauthorized)**

```json
{
  "success": false,
  "message": "Invalid email or password",
  "error": "Authentication failed"
}
```

**Error (400 Bad Request)**

```json
{
  "success": false,
  "message": "Validation error",
  "error": "\"email\" must be a valid email"
}
```

### Example Usage

```javascript
// Login request
const loginUser = async (email, password) => {
  const response = await fetch('/api/auth/login', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      email,
      password
    })
  });

  const data = await response.json();

  if (data.success) {
    // Store token for future requests
    localStorage.setItem('authToken', data.data.token);
    localStorage.setItem('refreshToken', data.data.refreshToken);
    return data.data.user;
  } else {
    throw new Error(data.message);
  }
};

// Register new user (HR/Admin only)
const registerUser = async (userData, adminToken) => {
  const response = await fetch('/api/auth/signup', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'Authorization': `Bearer ${adminToken}`
    },
    body: JSON.stringify(userData)
  });

  const data = await response.json();

  if (data.success) {
    return data.data.user;
  } else {
    throw new Error(data.message || data.errors?.map(e => e.message).join(', '));
  }
};

// Forgot password request
const forgotPassword = async (email) => {
  const response = await fetch('/api/auth/forgot-password', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({ email })
  });

  const data = await response.json();

  if (data.success) {
    return data.message;
  } else {
    throw new Error(data.message);
  }
};

// Reset password
const resetPassword = async (token, newPassword, confirmPassword) => {
  const response = await fetch('/api/auth/reset-password', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      token,
      newPassword,
      confirmPassword
    })
  });

  const data = await response.json();

  if (data.success) {
    return data.message;
  } else {
    throw new Error(data.message || data.errors?.map(e => e.message).join(', '));
  }
};
```

## POST /auth/logout

Logout user and invalidate token.

### Request

```http
POST /api/auth/logout
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

### Response

**Success (200 OK)**

```json
{
  "success": true,
  "message": "Logout successful"
}
```

### Example Usage

```javascript
const response = await fetch('/api/auth/logout', {
  method: 'POST',
  headers: {
    'Authorization': `Bearer ${localStorage.getItem('authToken')}`
  }
});

if (response.ok) {
  localStorage.removeItem('authToken');
  localStorage.removeItem('refreshToken');
}
```

## POST /auth/refresh

Refresh expired JWT token using refresh token.

### Request

```http
POST /api/auth/refresh
Content-Type: application/json

{
  "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

### Response

**Success (200 OK)**

```json
{
  "success": true,
  "data": {
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "expiresIn": "24h"
  }
}
```

## POST /auth/forgot-password

Request a password reset token via email.

### Request

```http
POST /api/auth/forgot-password
Content-Type: application/json

{
  "email": "john.doe@company.com"
}
```

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| email | string | Yes | User's email address |

### Response

**Success (200 OK)**

```json
{
  "success": true,
  "message": "Password reset email sent successfully",
  "data": {
    "email": "john.doe@company.com",
    "resetTokenExpires": "2024-02-20T11:00:00Z"
  }
}
```

**Error (404 Not Found)**

```json
{
  "success": false,
  "message": "User with this email address not found"
}
```

## POST /auth/reset-password

Reset password using the token received via email.

### Request

```http
POST /api/auth/reset-password
Content-Type: application/json

{
  "token": "abc123def456ghi789",
  "newPassword": "newSecurePassword123",
  "confirmPassword": "newSecurePassword123"
}
```

### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| token | string | Yes | Password reset token from email |
| newPassword | string | Yes | New password (min 8 characters) |
| confirmPassword | string | Yes | Confirm new password |

### Response

**Success (200 OK)**

```json
{
  "success": true,
  "message": "Password reset successfully",
  "data": {
    "userId": "550e8400-e29b-41d4-a716-446655440000",
    "email": "john.doe@company.com",
    "resetAt": "2024-02-20T10:30:00Z"
  }
}
```

**Error (400 Bad Request)**

```json
{
  "success": false,
  "message": "Password reset failed",
  "errors": [
    {
      "field": "token",
      "message": "Invalid or expired reset token"
    },
    {
      "field": "confirmPassword",
      "message": "Passwords do not match"
    }
  ]
}
```

## Token Usage

After successful authentication, include the JWT token in all API requests:

```http
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

## Error Codes

| Code | Description |
|------|-------------|
| 400 | Bad Request - Invalid input data |
| 401 | Unauthorized - Invalid credentials or expired token |
| 403 | Forbidden - Access denied |
| 500 | Internal Server Error - Server error |

## Security Notes

- Tokens expire after 24 hours
- Use refresh tokens to get new access tokens
- Store tokens securely (not in localStorage for production)
- Always use HTTPS in production
- Implement proper logout to invalidate tokens
