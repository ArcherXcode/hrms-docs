# Dashboard API

The Dashboard API provides role-based analytics and metrics for different user types in the HRMS system.

## Endpoints Overview

| Method | Endpoint | Description | Access |
|--------|----------|-------------|---------|
| GET | `/dashboard` | Get role-based dashboard | All authenticated |
| GET | `/dashboard/stats` | Get detailed statistics | All authenticated |

## GET /dashboard

Get dashboard overview based on user role with relevant metrics and insights.

### Request

```http
GET /api/dashboard
Authorization: Bearer <token>
```

### Response Structure

The response varies based on the user's role:

## Employee Dashboard

For users with role `employee`:

### Response

```json
{
  "success": true,
  "data": {
    "role": "employee",
    "today": {
      "attendance": {
        "id": "att-001",
        "checkIn": "09:00:00",
        "checkOut": "17:30:00",
        "status": "present"
      },
      "checkedIn": true,
      "checkedOut": true
    },
    "attendance": {
      "thisMonth": {
        "total": 20,
        "present": 18,
        "absent": 1,
        "late": 1
      }
    },
    "leaves": {
      "recent": [
        {
          "id": "leave-001",
          "leaveType": "casual",
          "startDate": "2024-02-15",
          "endDate": "2024-02-16",
          "status": "approved"
        }
      ],
      "pending": 1
    },
    "expenses": {
      "pending": [
        {
          "id": "exp-001",
          "title": "Travel Expense",
          "amount": 150.00,
          "status": "pending"
        }
      ],
      "pendingCount": 1,
      "pendingAmount": 150.00
    },
    "timesheets": {
      "thisMonth": {
        "totalHours": 152,
        "entries": 18
      }
    },
    "notifications": {
      "unreadCount": 3
    },
    "onboarding": {
      "progress": 75,
      "status": "in_progress",
      "pendingTasks": 2
    }
  }
}
```

## Manager Dashboard

For users with role `manager`:

### Response

```json
{
  "success": true,
  "data": {
    "role": "manager",
    "team": {
      "size": 8,
      "present": 7,
      "absent": 0,
      "late": 1
    },
    "approvals": {
      "leaves": {
        "count": 3,
        "totalDays": 8
      },
      "expenses": {
        "count": 5,
        "totalAmount": 1250.00
      },
      "timesheets": {
        "count": 2,
        "totalHours": 16
      }
    },
    "performance": {
      "thisMonth": {
        "totalRecords": 160,
        "attendanceRate": 92
      }
    }
  }
}
```

## HR Dashboard

For users with role `hr`:

### Response

```json
{
  "success": true,
  "data": {
    "role": "hr",
    "employees": {
      "total": 45,
      "active": 42
    },
    "attendance": {
      "today": {
        "total": 42,
        "present": 38,
        "absent": 2,
        "rate": 90
      }
    },
    "pendingTasks": {
      "leaveApprovals": 8,
      "onboardingActive": 3
    },
    "payroll": {
      "thisMonth": {
        "processed": 35,
        "totalAmount": 2450000
      }
    },
    "documents": {
      "recentUploads": 12
    }
  }
}
```

## Admin Dashboard

For users with role `admin`:

### Response

```json
{
  "success": true,
  "data": {
    "role": "admin",
    "system": {
      "totalUsers": 45,
      "activeUsers": 42,
      "totalDepartments": 8,
      "systemHealth": "good"
    },
    "financials": {
      "thisMonth": {
        "expenses": 25000,
        "payroll": 2450000
      }
    },
    "health": {
      "recentErrors": 0
    }
  }
}
```

## GET /dashboard/stats

Get detailed statistics for specific metrics with customizable time periods.

### Request

```http
GET /api/dashboard/stats?metric=attendance&period=month&startDate=2024-01-01&endDate=2024-01-31
Authorization: Bearer <token>
```

### Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| metric | string | Yes | Metric type: attendance, leaves, expenses, timesheets |
| period | string | No | Time period: day, week, month, quarter, year |
| startDate | date | No | Custom start date |
| endDate | date | No | Custom end date |

### Available Metrics

#### Attendance Statistics

```http
GET /api/dashboard/stats?metric=attendance&period=month
```

**Response:**

```json
{
  "success": true,
  "data": {
    "stats": {
      "total": 20,
      "present": 18,
      "absent": 1,
      "late": 1
    },
    "metric": "attendance",
    "period": "month"
  }
}
```

#### Leave Statistics

```http
GET /api/dashboard/stats?metric=leaves&period=quarter
```

**Response:**

```json
{
  "success": true,
  "data": {
    "stats": {
      "total": 12,
      "pending": 2,
      "approved": 8,
      "rejected": 2
    },
    "metric": "leaves",
    "period": "quarter"
  }
}
```

#### Expense Statistics

```http
GET /api/dashboard/stats?metric=expenses&period=month
```

**Response:**

```json
{
  "success": true,
  "data": {
    "stats": {
      "total": 8,
      "totalAmount": 2100.00,
      "pending": 3,
      "approved": 5
    },
    "metric": "expenses",
    "period": "month"
  }
}
```

#### Timesheet Statistics

```http
GET /api/dashboard/stats?metric=timesheets&period=week
```

**Response:**

```json
{
  "success": true,
  "data": {
    "stats": {
      "total": 5,
      "totalHours": 40,
      "billableHours": 32
    },
    "metric": "timesheets",
    "period": "week"
  }
}
```

## Role-Based Access

| Role | Dashboard Access | Stats Access | Notes |
|------|-----------------|-------------|-------|
| Employee | Personal data only | Own data only | Can see personal metrics |
| Manager | Team data | Team data | Can see team member statistics |
| HR | Organization-wide | Organization-wide | Full access to all employee data |
| Admin | System-wide | System-wide | Complete system overview |

## Error Responses

### Invalid Metric (400)

```json
{
  "success": false,
  "message": "Invalid metric specified"
}
```

### Missing Required Parameter (400)

```json
{
  "success": false,
  "message": "Metric parameter is required"
}
```

### Access Denied (403)

```json
{
  "success": false,
  "message": "Access denied"
}
```

## Usage Examples

### JavaScript

```javascript
// Get dashboard data
const getDashboard = async () => {
  const response = await fetch('/api/dashboard', {
    headers: {
      'Authorization': `Bearer ${token}`
    }
  });
  return response.json();
};

// Get attendance statistics for current month
const getAttendanceStats = async () => {
  const response = await fetch('/api/dashboard/stats?metric=attendance&period=month', {
    headers: {
      'Authorization': `Bearer ${token}`
    }
  });
  return response.json();
};

// Get custom period statistics
const getCustomStats = async (metric, startDate, endDate) => {
  const url = new URL('/api/dashboard/stats', window.location.origin);
  url.searchParams.set('metric', metric);
  url.searchParams.set('period', 'custom');
  url.searchParams.set('startDate', startDate);
  url.searchParams.set('endDate', endDate);

  const response = await fetch(url, {
    headers: {
      'Authorization': `Bearer ${token}`
    }
  });
  return response.json();
};
```

### React Hook Example

```javascript
import { useState, useEffect } from 'react';

const useDashboard = () => {
  const [dashboardData, setDashboardData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchDashboard = async () => {
      try {
        setLoading(true);
        const response = await fetch('/api/dashboard', {
          headers: {
            'Authorization': `Bearer ${localStorage.getItem('token')}`
          }
        });
        
        if (!response.ok) {
          throw new Error('Failed to fetch dashboard data');
        }
        
        const data = await response.json();
        setDashboardData(data.data);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    };

    fetchDashboard();
  }, []);

  return { dashboardData, loading, error };
};
```

### cURL Examples

```bash
# Get dashboard
curl -X GET "https://api.company.com/api/dashboard" \
  -H "Authorization: Bearer <token>"

# Get attendance stats
curl -X GET "https://api.company.com/api/dashboard/stats?metric=attendance&period=month" \
  -H "Authorization: Bearer <token>"

# Get custom period stats
curl -X GET "https://api.company.com/api/dashboard/stats?metric=expenses&period=custom&startDate=2024-01-01&endDate=2024-01-31" \
  -H "Authorization: Bearer <token>"
```

## Data Refresh

Dashboard data is real-time and reflects the current state of the system. For optimal performance:

- Cache dashboard data for short periods (5-10 minutes)
- Use WebSocket connections for real-time updates
- Implement polling for critical metrics
- Consider using React Query or SWR for data management
