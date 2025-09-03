# 🔐 Authentication

The Upsurge HRMS API uses JWT (JSON Web Tokens) for authentication. This guide covers everything you need to know about authenticating with our API.

## Overview

- **Authentication Type**: JWT Bearer Token
- **Token Location**: Authorization header
- **Token Lifetime**: 24 hours (configurable)
- **Refresh Token**: 7 days (configurable)

## Authentication Flow

### 1. Initial Login

**Endpoint:** `POST /auth/login`

```bash
curl -X POST "https://api.upsurgemedia.in/api/v1/auth/login" \
  -H "Content-Type: application/json" \
  -d '{
    "email": "user@company.com",
    "password": "userpassword"
  }'
```

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
  "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJ1c2VyLWlkIiwiaWF0IjoxNjk0NTI3MjAwLCJleHAiOjE2OTQ2MTM2MDB9.signature",
  "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJ1c2VyLWlkIiwiaWF0IjoxNjk0NTI3MjAwLCJleHAiOjE2OTUxMzIwMDB9.signature",
  "user": {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "email": "user@company.com",
    "fullName": "John Doe",
    "role": "employee",
    "companyId": "company-uuid-here",
    "departmentId": "dept-uuid-here",
    "isActive": true
  }
}
```

### 2. Using Access Tokens

Include the access token in the `Authorization` header for all subsequent requests:

```bash
curl -X GET "https://api.upsurgemedia.in/api/v1/employees" \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
```

### 3. Token Refresh

When your access token expires, use the refresh token to get a new one:

**Endpoint:** `POST /auth/refresh`

```bash
curl -X POST "https://api.upsurgemedia.in/api/v1/auth/refresh" \
  -H "Content-Type: application/json" \
  -d '{
    "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
  }'
```

**Response:**
```json
{
  "accessToken": "new-access-token-here",
  "refreshToken": "new-refresh-token-here"
}
```

## Role-Based Access Control (RBAC)

The API implements a comprehensive role-based permission system:

### User Roles

| Role | Description | Permissions |
|------|-------------|-------------|
| `superadmin` | System administrator | Full system access across all companies |
| `admin` | Company administrator | Full access within their company |
| `hr` | HR Manager | Employee management, payroll, reports |
| `manager` | Department Manager | Team management, approvals |
| `employee` | Regular Employee | Personal data, attendance, leave requests |

### Permission Examples

#### Super Admin
- Access all companies
- Manage system settings
- Create/delete companies
- Full CRUD on all entities

#### Company Admin
- Manage company settings
- Full employee management
- Financial data access
- Department management

#### HR Manager
- Employee lifecycle management
- Payroll processing
- Generate reports
- Leave approvals

#### Manager
- View team members
- Approve leave requests
- Team performance data
- Department reports

#### Employee
- Personal profile management
- Attendance tracking
- Leave applications
- Personal documents

## Token Structure

### Access Token Payload

```json
{
  "sub": "user-uuid",
  "email": "user@company.com",
  "role": "employee",
  "companyId": "company-uuid",
  "departmentId": "department-uuid",
  "iat": 1694527200,
  "exp": 1694613600
}
```

### Claims Explanation

- `sub`: Subject (User ID)
- `email`: User's email address
- `role`: User's role in the system
- `companyId`: Associated company
- `departmentId`: Associated department
- `iat`: Issued at timestamp
- `exp`: Expiration timestamp

## Security Best Practices

### 1. Token Storage

**✅ Recommended:**
- Store tokens in secure HTTP-only cookies
- Use secure storage mechanisms in mobile apps
- Implement token encryption for sensitive applications

**❌ Avoid:**
- Storing tokens in localStorage (XSS vulnerability)
- Including tokens in URLs
- Logging tokens in application logs

### 2. Token Refresh Strategy

```javascript
class AuthService {
  constructor() {
    this.accessToken = null;
    this.refreshToken = null;
    this.refreshPromise = null;
  }

  async refreshAccessToken() {
    if (this.refreshPromise) {
      return this.refreshPromise;
    }

    this.refreshPromise = fetch('/auth/refresh', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ refreshToken: this.refreshToken })
    })
    .then(response => response.json())
    .then(data => {
      this.accessToken = data.accessToken;
      this.refreshToken = data.refreshToken;
      this.refreshPromise = null;
      return data.accessToken;
    })
    .catch(error => {
      this.refreshPromise = null;
      throw error;
    });

    return this.refreshPromise;
  }

  async makeAuthenticatedRequest(url, options = {}) {
    try {
      const response = await fetch(url, {
        ...options,
        headers: {
          'Authorization': `Bearer ${this.accessToken}`,
          ...options.headers
        }
      });

      if (response.status === 401) {
        // Token expired, try to refresh
        await this.refreshAccessToken();
        
        // Retry the original request
        return fetch(url, {
          ...options,
          headers: {
            'Authorization': `Bearer ${this.accessToken}`,
            ...options.headers
          }
        });
      }

      return response;
    } catch (error) {
      console.error('Authentication error:', error);
      throw error;
    }
  }
}
```

### 3. Logout

**Endpoint:** `POST /auth/logout`

```bash
curl -X POST "https://api.upsurgemedia.in/api/v1/auth/logout" \
  -H "Authorization: Bearer YOUR_ACCESS_TOKEN"
```

This endpoint invalidates the current token and clears server-side session data.

## Error Handling

### Common Authentication Errors

#### 401 Unauthorized
```json
{
  "error": {
    "code": "UNAUTHORIZED",
    "message": "Invalid or expired token",
    "statusCode": 401
  },
  "timestamp": "2025-09-03T10:00:00Z"
}
```

**Resolution**: Refresh your token or re-authenticate.

#### 403 Forbidden
```json
{
  "error": {
    "code": "FORBIDDEN",
    "message": "Insufficient permissions for this resource",
    "statusCode": 403
  },
  "timestamp": "2025-09-03T10:00:00Z"
}
```

**Resolution**: Contact your administrator for proper role assignment.

#### 422 Validation Error
```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid email or password",
    "statusCode": 422
  },
  "timestamp": "2025-09-03T10:00:00Z"
}
```

**Resolution**: Check your credentials and try again.

## Integration Examples

### React/JavaScript

```javascript
import axios from 'axios';

// Configure axios interceptor for automatic token handling
const api = axios.create({
  baseURL: 'https://api.upsurgemedia.in'
});

// Request interceptor to add token
api.interceptors.request.use(
  (config) => {
    const token = localStorage.getItem('accessToken');
    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }
    return config;
  },
  (error) => Promise.reject(error)
);

// Response interceptor to handle token refresh
api.interceptors.response.use(
  (response) => response,
  async (error) => {
    const originalRequest = error.config;

    if (error.response?.status === 401 && !originalRequest._retry) {
      originalRequest._retry = true;

      try {
        const refreshToken = localStorage.getItem('refreshToken');
        const response = await axios.post('/auth/refresh', { refreshToken });
        
        const { accessToken } = response.data;
        localStorage.setItem('accessToken', accessToken);
        
        originalRequest.headers.Authorization = `Bearer ${accessToken}`;
        return api(originalRequest);
      } catch (refreshError) {
        // Refresh failed, redirect to login
        localStorage.removeItem('accessToken');
        localStorage.removeItem('refreshToken');
        window.location.href = '/login';
      }
    }

    return Promise.reject(error);
  }
);

export default api;
```

### Python

```python
import requests
import time
from datetime import datetime, timedelta

class UpsurgeAPI:
    def __init__(self, base_url="https://api.upsurgemedia.in"):
        self.base_url = base_url
        self.access_token = None
        self.refresh_token = None
        self.token_expires_at = None

    def login(self, email, password):
        response = requests.post(
            f"{self.base_url}/auth/login",
            json={"email": email, "password": password}
        )
        
        if response.status_code == 200:
            data = response.json()
            self.access_token = data["accessToken"]
            self.refresh_token = data["refreshToken"]
            
            # Set expiration time (24 hours from now)
            self.token_expires_at = datetime.now() + timedelta(hours=24)
            
            return data["user"]
        else:
            raise Exception(f"Login failed: {response.text}")

    def refresh_access_token(self):
        if not self.refresh_token:
            raise Exception("No refresh token available")

        response = requests.post(
            f"{self.base_url}/auth/refresh",
            json={"refreshToken": self.refresh_token}
        )
        
        if response.status_code == 200:
            data = response.json()
            self.access_token = data["accessToken"]
            self.refresh_token = data["refreshToken"]
            self.token_expires_at = datetime.now() + timedelta(hours=24)
        else:
            raise Exception(f"Token refresh failed: {response.text}")

    def make_request(self, method, endpoint, **kwargs):
        # Check if token needs refresh
        if (self.token_expires_at and 
            datetime.now() >= self.token_expires_at - timedelta(minutes=5)):
            self.refresh_access_token()

        headers = kwargs.pop('headers', {})
        headers['Authorization'] = f'Bearer {self.access_token}'
        
        response = requests.request(
            method,
            f"{self.base_url}{endpoint}",
            headers=headers,
            **kwargs
        )
        
        if response.status_code == 401:
            # Try refreshing token once
            self.refresh_access_token()
            headers['Authorization'] = f'Bearer {self.access_token}'
            
            response = requests.request(
                method,
                f"{self.base_url}{endpoint}",
                headers=headers,
                **kwargs
            )
        
        return response

# Usage example
api = UpsurgeAPI()
user = api.login("user@company.com", "password")

# Make authenticated requests
employees = api.make_request("GET", "/employees").json()
```

## Testing Authentication

### Using curl

```bash
# 1. Login and save response
TOKEN_RESPONSE=$(curl -s -X POST "https://api.upsurgemedia.in/api/v1/auth/login" \
  -H "Content-Type: application/json" \
  -d '{
    "email": "admin@upsurgemedia.com",
    "password": "password123"
  }')

# 2. Extract access token
ACCESS_TOKEN=$(echo $TOKEN_RESPONSE | jq -r '.accessToken')

# 3. Make authenticated request
curl -X GET "https://api.upsurgemedia.in/api/v1/employees" \
  -H "Authorization: Bearer $ACCESS_TOKEN"
```

### Using Postman

1. **Set Environment Variables:**
   - `base_url`: `https://api.upsurgemedia.in`
   - `access_token`: (will be set automatically)

2. **Create Login Request:**
   - Method: POST
   - URL: `{{base_url}}/auth/login`
   - Body: JSON with email and password

3. **Add Test Script:**
   ```javascript
   if (pm.response.code === 200) {
       const response = pm.response.json();
       pm.environment.set("access_token", response.accessToken);
   }
   ```

4. **Use Token in Other Requests:**
   - Authorization: Bearer Token
   - Token: `{{access_token}}`

---

This comprehensive authentication system ensures secure access to all API endpoints while maintaining flexibility for different client implementations.
