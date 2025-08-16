# Quick Start Guide

This guide helps you get started with the HRMS API quickly.

## Prerequisites

- Valid API credentials (email/password)
- API base URL: `https://api.your-domain.com/api`
- HTTP client (curl, Postman, or programming language)

## Step 1: Authentication

First, obtain an access token by logging in:

```bash
curl -X POST "https://api.your-domain.com/api/auth/login" \
  -H "Content-Type: application/json" \
  -d '{
    "email": "your.email@company.com",
    "password": "yourPassword"
  }'
```

**Response:**
```json
{
  "success": true,
  "data": {
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "user": {
      "id": "550e8400-e29b-41d4-a716-446655440000",
      "role": "employee"
    }
  }
}
```

Save the token for subsequent requests.

## Step 2: Make Your First API Call

Use the token to fetch your dashboard:

```bash
curl -X GET "https://api.your-domain.com/api/dashboard" \
  -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
```

## Step 3: Common Operations

### List Employees
```bash
curl -X GET "https://api.your-domain.com/api/employees" \
  -H "Authorization: Bearer <token>"
```

### Submit Leave Request
```bash
curl -X POST "https://api.your-domain.com/api/leave" \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{
    "leaveType": "casual",
    "startDate": "2024-03-15",
    "endDate": "2024-03-16",
    "reason": "Personal work"
  }'
```

### Check Attendance
```bash
curl -X GET "https://api.your-domain.com/api/attendance" \
  -H "Authorization: Bearer <token>"
```

## Next Steps

- Explore the [Dashboard API](dashboard.md) for analytics
- Learn about [Employee Management](employees.md)
- Set up [Leave Management](leave.md) workflows
- Integrate [Notifications](notifications.md)

## SDKs and Libraries

### JavaScript/Node.js
```javascript
const response = await fetch('/api/dashboard', {
  headers: {
    'Authorization': `Bearer ${token}`
  }
});
const data = await response.json();
```

### Python
```python
import requests

headers = {'Authorization': f'Bearer {token}'}
response = requests.get('/api/dashboard', headers=headers)
data = response.json()
```

### PHP
```php
$headers = ['Authorization: Bearer ' . $token];
$response = file_get_contents('/api/dashboard', false, stream_context_create([
    'http' => ['header' => implode("\r\n", $headers)]
]));
$data = json_decode($response, true);
```

## Error Handling

Always check the response status and handle errors:

```javascript
try {
  const response = await fetch('/api/employees');
  if (!response.ok) {
    throw new Error(`HTTP ${response.status}: ${response.statusText}`);
  }
  const data = await response.json();
  
  if (!data.success) {
    throw new Error(data.message);
  }
  
  // Process data.data
} catch (error) {
  console.error('API Error:', error.message);
}
```

## Rate Limits

The API has rate limits to ensure fair usage:
- 1000 requests per hour per user
- 100 requests per minute per endpoint
- Headers indicate remaining quota

## Support

- 📧 Email: api-support@your-domain.com
- 📖 Documentation: This guide
- 🐛 Issues: GitHub repository
