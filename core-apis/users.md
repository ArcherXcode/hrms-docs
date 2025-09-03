# 👥 Users API

The Users API provides comprehensive user management functionality including user creation, authentication, profile management, and role assignments.

## Base Endpoint

```
https://api.upsurgemedia.in/api/v1/users
```

## Overview

User management is central to the HRMS system, providing authentication, authorization, and profile management capabilities. Users are linked to employees and have specific roles that determine their access permissions.

## User Model

```typescript
interface User {
  id: string;              // UUID
  email: string;           // Unique email address
  fullName: string;        // Full display name
  role: UserRole;          // System role
  isActive: boolean;       // Account status
  lastLoginAt?: Date;      // Last login timestamp
  createdAt: Date;         // Creation timestamp
  updatedAt: Date;         // Last update timestamp
  companyId: string;       // Associated company
  
  // Relationships
  company?: Company;       // Company details
  employee?: Employee;     // Employee profile (if applicable)
  notifications?: Notification[]; // User notifications
}

enum UserRole {
  SUPERADMIN = 'superadmin',
  ADMIN = 'admin',
  HR = 'hr', 
  MANAGER = 'manager',
  EMPLOYEE = 'employee'
}
```

## Authentication Endpoints

### Login

Authenticate a user and receive JWT tokens.

**Endpoint:** `POST /auth/login`

**Request Body:**
```json
{
  "email": "user@company.com",
  "password": "userpassword"
}
```

**Response:**
```json
{
  "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "user": {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "email": "user@company.com",
    "fullName": "John Doe",
    "role": "employee",
    "isActive": true,
    "companyId": "company-uuid",
    "lastLoginAt": "2025-09-03T10:00:00Z"
  }
}
```

**Example:**
```bash
curl -X POST "https://api.upsurgemedia.in/api/v1/auth/login" \
  -H "Content-Type: application/json" \
  -d '{
    "email": "admin@upsurgemedia.com",
    "password": "password123"
  }'
```

### Refresh Token

Refresh an expired access token using a refresh token.

**Endpoint:** `POST /auth/refresh`

**Request Body:**
```json
{
  "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

**Response:**
```json
{
  "accessToken": "new-access-token",
  "refreshToken": "new-refresh-token"
}
```

### Logout

Invalidate current session and tokens.

**Endpoint:** `POST /auth/logout`

**Headers:** `Authorization: Bearer <token>`

**Response:**
```json
{
  "message": "Successfully logged out",
  "timestamp": "2025-09-03T10:00:00Z"
}
```

### Change Password

Change user password (requires current password).

**Endpoint:** `POST /auth/change-password`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "currentPassword": "oldpassword",
  "newPassword": "newpassword123",
  "confirmPassword": "newpassword123"
}
```

**Response:**
```json
{
  "message": "Password changed successfully",
  "timestamp": "2025-09-03T10:00:00Z"
}
```

## User Management Endpoints

### Get All Users

Retrieve a paginated list of users (Admin/HR only).

**Endpoint:** `GET /users`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `page` (number): Page number (default: 1)
- `limit` (number): Items per page (default: 10, max: 100)
- `role` (string): Filter by role
- `isActive` (boolean): Filter by active status
- `companyId` (string): Filter by company
- `search` (string): Search by name or email

**Response:**
```json
{
  "data": [
    {
      "id": "user-uuid",
      "email": "john.doe@company.com",
      "fullName": "John Doe",
      "role": "employee",
      "isActive": true,
      "lastLoginAt": "2025-09-03T09:30:00Z",
      "createdAt": "2025-08-15T08:00:00Z",
      "company": {
        "id": "company-uuid",
        "name": "Upsurge Media"
      },
      "employee": {
        "id": "emp-uuid",
        "employeeId": "EMP001"
      }
    }
  ],
  "total": 25,
  "page": 1,
  "limit": 10,
  "totalPages": 3
}
```

**Example:**
```bash
curl -X GET "https://api.upsurgemedia.in/api/v1/users?role=employee&isActive=true&limit=20" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

### Get User by ID

Retrieve a specific user by ID.

**Endpoint:** `GET /users/{id}`

**Headers:** `Authorization: Bearer <token>`

**Response:**
```json
{
  "data": {
    "id": "user-uuid",
    "email": "john.doe@company.com",
    "fullName": "John Doe",
    "role": "employee",
    "isActive": true,
    "lastLoginAt": "2025-09-03T09:30:00Z",
    "createdAt": "2025-08-15T08:00:00Z",
    "updatedAt": "2025-09-01T10:00:00Z",
    "company": {
      "id": "company-uuid",
      "name": "Upsurge Media",
      "domain": "upsurgemedia.com"
    },
    "employee": {
      "id": "emp-uuid",
      "employeeId": "EMP001",
      "jobTitle": "Software Developer"
    }
  }
}
```

### Get Current User Profile

Get the authenticated user's profile information.

**Endpoint:** `GET /users/profile`

**Headers:** `Authorization: Bearer <token>`

**Response:**
```json
{
  "data": {
    "id": "user-uuid",
    "email": "john.doe@company.com",
    "fullName": "John Doe",
    "role": "employee",
    "isActive": true,
    "lastLoginAt": "2025-09-03T09:30:00Z",
    "company": {
      "name": "Upsurge Media"
    },
    "employee": {
      "employeeId": "EMP001",
      "jobTitle": "Software Developer",
      "department": {
        "name": "Engineering"
      }
    }
  }
}
```

### Create User

Create a new user account (Admin/HR only).

**Endpoint:** `POST /users`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "email": "newuser@company.com",
  "fullName": "Jane Smith",
  "role": "employee",
  "password": "temporarypassword123",
  "companyId": "company-uuid",
  "isActive": true
}
```

**Response:**
```json
{
  "data": {
    "id": "new-user-uuid",
    "email": "newuser@company.com",
    "fullName": "Jane Smith",
    "role": "employee",
    "isActive": true,
    "createdAt": "2025-09-03T10:00:00Z",
    "companyId": "company-uuid"
  },
  "message": "User created successfully"
}
```

**Example:**
```bash
curl -X POST "https://api.upsurgemedia.in/api/v1/users" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "email": "jane.smith@upsurgemedia.com",
    "fullName": "Jane Smith",
    "role": "employee",
    "password": "welcome123",
    "companyId": "company-uuid"
  }'
```

### Update User

Update user information (Admin/HR or self-update).

**Endpoint:** `PATCH /users/{id}`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "fullName": "Jane Smith-Johnson",
  "role": "manager",
  "isActive": false
}
```

**Response:**
```json
{
  "data": {
    "id": "user-uuid",
    "email": "jane.smith@company.com",
    "fullName": "Jane Smith-Johnson",
    "role": "manager",
    "isActive": false,
    "updatedAt": "2025-09-03T10:00:00Z"
  },
  "message": "User updated successfully"
}
```

### Update Profile

Update the authenticated user's profile.

**Endpoint:** `PATCH /users/profile`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "fullName": "John Michael Doe"
}
```

**Response:**
```json
{
  "data": {
    "id": "user-uuid",
    "email": "john.doe@company.com",
    "fullName": "John Michael Doe",
    "updatedAt": "2025-09-03T10:00:00Z"
  },
  "message": "Profile updated successfully"
}
```

### Delete User

Soft delete a user account (Admin only).

**Endpoint:** `DELETE /users/{id}`

**Headers:** `Authorization: Bearer <token>`

**Response:**
```json
{
  "message": "User deleted successfully",
  "timestamp": "2025-09-03T10:00:00Z"
}
```

## User Statistics

### Get User Statistics

Get user statistics for dashboard (Admin/HR only).

**Endpoint:** `GET /users/statistics`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `companyId` (string): Filter by company
- `period` (string): Time period (today, week, month, year)

**Response:**
```json
{
  "data": {
    "totalUsers": 125,
    "activeUsers": 118,
    "inactiveUsers": 7,
    "onlineUsers": 45,
    "newUsersThisMonth": 8,
    "usersByRole": {
      "admin": 2,
      "hr": 3,
      "manager": 15,
      "employee": 105
    },
    "loginActivity": [
      {
        "date": "2025-09-01",
        "logins": 89
      },
      {
        "date": "2025-09-02", 
        "logins": 95
      }
    ]
  }
}
```

## Role Management

### Get Available Roles

Get list of available user roles.

**Endpoint:** `GET /users/roles`

**Headers:** `Authorization: Bearer <token>`

**Response:**
```json
{
  "data": [
    {
      "value": "superadmin",
      "label": "Super Administrator",
      "description": "Full system access"
    },
    {
      "value": "admin",
      "label": "Company Administrator", 
      "description": "Company-wide management"
    },
    {
      "value": "hr",
      "label": "HR Manager",
      "description": "Employee and HR management"
    },
    {
      "value": "manager",
      "label": "Department Manager",
      "description": "Team management"
    },
    {
      "value": "employee",
      "label": "Employee",
      "description": "Basic employee access"
    }
  ]
}
```

### Update User Role

Update a user's role (Admin only).

**Endpoint:** `PATCH /users/{id}/role`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "role": "manager"
}
```

**Response:**
```json
{
  "data": {
    "id": "user-uuid",
    "role": "manager",
    "updatedAt": "2025-09-03T10:00:00Z"
  },
  "message": "User role updated successfully"
}
```

## Bulk Operations

### Bulk Create Users

Create multiple users at once (Admin/HR only).

**Endpoint:** `POST /users/bulk`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "users": [
    {
      "email": "user1@company.com",
      "fullName": "User One",
      "role": "employee",
      "password": "temp123"
    },
    {
      "email": "user2@company.com", 
      "fullName": "User Two",
      "role": "employee",
      "password": "temp123"
    }
  ],
  "companyId": "company-uuid"
}
```

**Response:**
```json
{
  "data": {
    "created": 2,
    "failed": 0,
    "results": [
      {
        "email": "user1@company.com",
        "status": "created",
        "id": "user1-uuid"
      },
      {
        "email": "user2@company.com",
        "status": "created", 
        "id": "user2-uuid"
      }
    ]
  },
  "message": "Bulk user creation completed"
}
```

### Bulk Update Users

Update multiple users at once (Admin/HR only).

**Endpoint:** `PATCH /users/bulk`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "updates": [
    {
      "id": "user1-uuid",
      "isActive": false
    },
    {
      "id": "user2-uuid",
      "role": "manager"
    }
  ]
}
```

**Response:**
```json
{
  "data": {
    "updated": 2,
    "failed": 0,
    "results": [
      {
        "id": "user1-uuid",
        "status": "updated"
      },
      {
        "id": "user2-uuid",
        "status": "updated"
      }
    ]
  },
  "message": "Bulk user update completed"
}
```

## User Activity

### Get User Activity Log

Get activity log for a user (Admin/HR only).

**Endpoint:** `GET /users/{id}/activity`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `page` (number): Page number
- `limit` (number): Items per page
- `action` (string): Filter by action type

**Response:**
```json
{
  "data": [
    {
      "id": "activity-uuid",
      "action": "login",
      "description": "User logged in",
      "ipAddress": "192.168.1.100",
      "userAgent": "Mozilla/5.0...",
      "timestamp": "2025-09-03T09:30:00Z"
    },
    {
      "id": "activity-uuid-2",
      "action": "profile_update",
      "description": "Updated profile information",
      "timestamp": "2025-09-02T14:20:00Z"
    }
  ],
  "total": 45,
  "page": 1,
  "limit": 10
}
```

## Error Responses

### Common Error Codes

#### 400 Bad Request
```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid email format",
    "statusCode": 400
  }
}
```

#### 401 Unauthorized
```json
{
  "error": {
    "code": "UNAUTHORIZED",
    "message": "Invalid credentials",
    "statusCode": 401
  }
}
```

#### 403 Forbidden
```json
{
  "error": {
    "code": "FORBIDDEN",
    "message": "Insufficient permissions to access this resource",
    "statusCode": 403
  }
}
```

#### 404 Not Found
```json
{
  "error": {
    "code": "USER_NOT_FOUND",
    "message": "User with ID 'uuid' not found",
    "statusCode": 404
  }
}
```

#### 422 Validation Error
```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Email already exists",
    "details": {
      "field": "email",
      "value": "user@company.com"
    },
    "statusCode": 422
  }
}
```

## Integration Examples

### JavaScript/React

```javascript
import axios from 'axios';

class UserService {
  constructor(baseURL = 'https://api.upsurgemedia.in') {
    this.api = axios.create({ baseURL });
    this.token = localStorage.getItem('accessToken');
    
    if (this.token) {
      this.api.defaults.headers.common['Authorization'] = `Bearer ${this.token}`;
    }
  }

  async login(email, password) {
    const response = await this.api.post('/auth/login', { email, password });
    this.token = response.data.accessToken;
    localStorage.setItem('accessToken', this.token);
    this.api.defaults.headers.common['Authorization'] = `Bearer ${this.token}`;
    return response.data;
  }

  async getProfile() {
    const response = await this.api.get('/users/profile');
    return response.data.data;
  }

  async updateProfile(data) {
    const response = await this.api.patch('/users/profile', data);
    return response.data.data;
  }

  async getUsers(params = {}) {
    const response = await this.api.get('/users', { params });
    return response.data;
  }

  async createUser(userData) {
    const response = await this.api.post('/users', userData);
    return response.data.data;
  }

  async updateUser(id, updates) {
    const response = await this.api.patch(`/users/${id}`, updates);
    return response.data.data;
  }

  async deleteUser(id) {
    await this.api.delete(`/users/${id}`);
  }

  logout() {
    this.api.post('/auth/logout').catch(console.error);
    localStorage.removeItem('accessToken');
    delete this.api.defaults.headers.common['Authorization'];
  }
}

// Usage
const userService = new UserService();

// Login
const loginResult = await userService.login('user@company.com', 'password');
console.log('Logged in:', loginResult.user);

// Get current user profile
const profile = await userService.getProfile();
console.log('Profile:', profile);

// Get all users (admin only)
const users = await userService.getUsers({ role: 'employee', limit: 20 });
console.log('Users:', users.data);
```

### Python

```python
import requests
from typing import Optional, Dict, List

class UserAPI:
    def __init__(self, base_url: str = "https://api.upsurgemedia.in"):
        self.base_url = base_url
        self.token: Optional[str] = None
        self.session = requests.Session()

    def _headers(self) -> Dict[str, str]:
        headers = {"Content-Type": "application/json"}
        if self.token:
            headers["Authorization"] = f"Bearer {self.token}"
        return headers

    def login(self, email: str, password: str) -> Dict:
        response = self.session.post(
            f"{self.base_url}/auth/login",
            json={"email": email, "password": password},
            headers={"Content-Type": "application/json"}
        )
        response.raise_for_status()
        
        data = response.json()
        self.token = data["accessToken"]
        return data

    def get_profile(self) -> Dict:
        response = self.session.get(
            f"{self.base_url}/users/profile",
            headers=self._headers()
        )
        response.raise_for_status()
        return response.json()["data"]

    def get_users(self, **params) -> Dict:
        response = self.session.get(
            f"{self.base_url}/users",
            params=params,
            headers=self._headers()
        )
        response.raise_for_status()
        return response.json()

    def create_user(self, user_data: Dict) -> Dict:
        response = self.session.post(
            f"{self.base_url}/users",
            json=user_data,
            headers=self._headers()
        )
        response.raise_for_status()
        return response.json()["data"]

    def update_user(self, user_id: str, updates: Dict) -> Dict:
        response = self.session.patch(
            f"{self.base_url}/users/{user_id}",
            json=updates,
            headers=self._headers()
        )
        response.raise_for_status()
        return response.json()["data"]

# Usage
api = UserAPI()

# Login
login_result = api.login("admin@upsurgemedia.com", "password123")
print(f"Logged in as: {login_result['user']['fullName']}")

# Get profile
profile = api.get_profile()
print(f"Profile: {profile['fullName']} ({profile['role']})")

# Get users
users = api.get_users(role="employee", isActive=True, limit=10)
print(f"Found {users['total']} users")
```

---

The Users API provides comprehensive user management capabilities with robust authentication, authorization, and profile management features. All endpoints include proper error handling and follow REST conventions for consistent integration.
