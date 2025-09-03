# 🚀 Quick Start Guide

Get up and running with the Upsurge HRMS API in just a few minutes.

## 📋 Prerequisites

Before you begin, ensure you have:
- A valid API account
- Basic knowledge of REST APIs
- HTTP client (curl, Postman, or programming language of choice)

## 🔐 Step 1: Authentication

### Get Your API Token

First, you need to authenticate and obtain a JWT token:

```bash
curl -X POST "https://api.upsurgemedia.in/api/v1/auth/login" \
  -H "Content-Type: application/json" \
  -d '{
    "email": "your-email@company.com",
    "password": "your-password"
  }'
```

**Response:**
```json
{
  "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "user": {
    "id": "uuid",
    "email": "your-email@company.com",
    "fullName": "Your Name",
    "role": "admin",
    "companyId": "company-uuid"
  }
}
```

### Test Accounts

For testing purposes, you can use these pre-configured accounts:

| Role | Email | Password |
|------|-------|----------|
| Super Admin | superadmin@system.com | password123 |
| Company Admin | admin@upsurgemedia.com | password123 |
| HR Manager | hr.manager@upsurgemedia.com | password123 |
| Employee | alice.wilson@upsurgemedia.com | password123 |

## 🎯 Step 2: Make Your First API Call

Using the token from Step 1, make your first authenticated request:

```bash
curl -X GET "https://api.upsurgemedia.in/api/v1/employees" \
  -H "Authorization: Bearer YOUR_ACCESS_TOKEN" \
  -H "Content-Type: application/json"
```

**Response:**
```json
{
  "data": [
    {
      "id": "employee-uuid",
      "employeeId": "EMP001",
      "user": {
        "fullName": "John Doe",
        "email": "john.doe@company.com"
      },
      "jobTitle": "Software Developer",
      "department": {
        "name": "Engineering"
      },
      "status": "active"
    }
  ],
  "total": 1,
  "page": 1,
  "limit": 10
}
```

## 📊 Step 3: Explore Key Endpoints

### Dashboard Data
Get role-based dashboard information:

```bash
curl -X GET "https://api.upsurgemedia.in/api/v1/dashboard/overview" \
  -H "Authorization: Bearer YOUR_ACCESS_TOKEN"
```

### Search Functionality
Search across employees, departments, and more:

```bash
curl -X GET "https://api.upsurgemedia.in/api/v1/search/global?q=john" \
  -H "Authorization: Bearer YOUR_ACCESS_TOKEN"
```

### Attendance Tracking
Record employee attendance:

```bash
curl -X POST "https://api.upsurgemedia.in/api/v1/attendance/check-in" \
  -H "Authorization: Bearer YOUR_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "location": "Office",
    "device": "Mobile App"
  }'
```

## 🏗️ Step 4: Understanding the Data Structure

### Common Response Format

All API responses follow a consistent structure:

```json
{
  "data": "...",          // The actual data
  "total": 100,           // Total count (for paginated responses)
  "page": 1,              // Current page
  "limit": 10,            // Items per page
  "message": "Success",   // Status message
  "timestamp": "2025-09-03T10:00:00Z"
}
```

### Error Responses

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

## 📱 Step 5: Common Use Cases

### 1. Employee Management Flow

```javascript
// 1. Create a new employee
const newEmployee = await fetch('/employees', {
  method: 'POST',
  headers: {
    'Authorization': `Bearer ${token}`,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    employeeId: 'EMP002',
    user: {
      email: 'new.employee@company.com',
      fullName: 'Jane Smith',
      role: 'employee'
    },
    jobTitle: 'Marketing Specialist',
    departmentId: 'dept-uuid',
    hireDate: '2025-09-03'
  })
});

// 2. Get employee details
const employee = await fetch(`/employees/${employeeId}`, {
  headers: { 'Authorization': `Bearer ${token}` }
});

// 3. Update employee information
const updated = await fetch(`/employees/${employeeId}`, {
  method: 'PATCH',
  headers: {
    'Authorization': `Bearer ${token}`,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    jobTitle: 'Senior Marketing Specialist'
  })
});
```

### 2. Attendance Tracking Flow

```javascript
// Morning check-in
const checkIn = await fetch('/attendance/check-in', {
  method: 'POST',
  headers: {
    'Authorization': `Bearer ${token}`,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    location: 'Main Office',
    device: 'Web Browser'
  })
});

// Evening check-out
const checkOut = await fetch('/attendance/check-out', {
  method: 'POST',
  headers: {
    'Authorization': `Bearer ${token}`,
    'Content-Type': 'application/json'
  }
});

// Get attendance history
const attendance = await fetch('/attendance?startDate=2025-09-01&endDate=2025-09-30', {
  headers: { 'Authorization': `Bearer ${token}` }
});
```

### 3. Leave Management Flow

```javascript
// Apply for leave
const leaveApplication = await fetch('/leave', {
  method: 'POST',
  headers: {
    'Authorization': `Bearer ${token}`,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    leaveTypeId: 'leave-type-uuid',
    startDate: '2025-09-15',
    endDate: '2025-09-17',
    reason: 'Personal vacation'
  })
});

// Get leave balance
const balance = await fetch('/leave/balance', {
  headers: { 'Authorization': `Bearer ${token}` }
});
```

## 🔧 Development Tips

### 1. Use Environment Variables
```bash
export API_BASE_URL="https://api.upsurgemedia.in"
export API_TOKEN="your-jwt-token"
```

### 2. Handle Token Refresh
```javascript
async function refreshToken(refreshToken) {
  const response = await fetch('/auth/refresh', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ refreshToken })
  });
  return response.json();
}
```

### 3. Error Handling
```javascript
async function apiCall(endpoint, options = {}) {
  try {
    const response = await fetch(`${API_BASE_URL}${endpoint}`, {
      ...options,
      headers: {
        'Authorization': `Bearer ${token}`,
        'Content-Type': 'application/json',
        ...options.headers
      }
    });

    if (!response.ok) {
      const error = await response.json();
      throw new Error(error.message);
    }

    return await response.json();
  } catch (error) {
    console.error('API Error:', error);
    throw error;
  }
}
```

## 📚 Next Steps

1. **Explore the API**: Check out our [API Overview](api-overview.md) for a complete list of endpoints
2. **Authentication Deep Dive**: Learn more about [Authentication](authentication.md) and security
3. **Integration Examples**: See real-world examples in our endpoint documentation
4. **Testing**: Use our Swagger UI at `https://api.upsurgemedia.in/api/docs` for interactive testing

## 🆘 Need Help?

- **Documentation**: Browse our comprehensive API documentation
- **Support**: Contact us at support@upsurgemedia.com
- **Community**: Join our developer community for tips and best practices

---

**🎉 Congratulations!** You're now ready to build amazing HR applications with the Upsurge HRMS API.
