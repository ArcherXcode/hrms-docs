# Attendance API

The Attendance API manages employee check-in/check-out operations, attendance tracking, and attendance-related statistics.

## Endpoints Overview

| Method | Endpoint | Description | Access |
|--------|----------|-------------|---------|
| GET | `/attendance` | List attendance records | All authenticated |
| GET | `/attendance/:id` | Get attendance record by ID | Owner, HR, Manager, Admin |
| POST | `/attendance/check-in` | Employee check-in | All authenticated |
| POST | `/attendance/check-out` | Employee check-out | All authenticated |
| PUT | `/attendance/:id` | Update attendance record | HR, Manager, Admin |
| DELETE | `/attendance/:id` | Delete attendance record | HR, Admin |
| GET | `/attendance/stats/overview` | Get attendance statistics | HR, Manager, Admin |

## GET /attendance

Retrieve attendance records with filtering and pagination.

### Request

```http
GET /api/attendance?page=1&limit=10&userId=user-001&date=2024-02-15&status=present
Authorization: Bearer <token>
```

### Query Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| page | number | 1 | Page number for pagination |
| limit | number | 10 | Number of records per page |
| userId | string | - | Filter by user ID (HR/Manager/Admin only) |
| date | date | - | Filter by specific date |
| startDate | date | - | Filter by date range start |
| endDate | date | - | Filter by date range end |
| status | string | - | Filter by status (present, absent, late) |

### Response

```json
{
  "success": true,
  "data": {
    "attendance": [
      {
        "id": "att-001",
        "userId": "user-001",
        "date": "2024-02-15",
        "checkIn": "09:15:00",
        "checkOut": "17:30:00",
        "status": "present",
        "workingHours": 8.25,
        "breakTime": 60,
        "overtime": 0,
        "notes": "Regular day",
        "user": {
          "id": "user-001",
          "firstName": "John",
          "lastName": "Doe",
          "employeeId": "EMP001"
        },
        "createdAt": "2024-02-15T09:15:00Z",
        "updatedAt": "2024-02-15T17:30:00Z"
      }
    ],
    "pagination": {
      "page": 1,
      "limit": 10,
      "total": 120,
      "pages": 12
    }
  }
}
```

## GET /attendance/:id

Get detailed information about a specific attendance record.

### Request

```http
GET /api/attendance/att-001
Authorization: Bearer <token>
```

### Response

```json
{
  "success": true,
  "data": {
    "attendance": {
      "id": "att-001",
      "userId": "user-001",
      "date": "2024-02-15",
      "checkIn": "09:15:00",
      "checkOut": "17:30:00",
      "status": "present",
      "workingHours": 8.25,
      "breakTime": 60,
      "overtime": 0,
      "location": "Office - Floor 3",
      "ipAddress": "192.168.1.100",
      "notes": "Regular working day",
      "user": {
        "id": "user-001",
        "firstName": "John",
        "lastName": "Doe",
        "employeeId": "EMP001",
        "department": {
          "id": "dept-001",
          "name": "Engineering"
        }
      },
      "createdAt": "2024-02-15T09:15:00Z",
      "updatedAt": "2024-02-15T17:30:00Z"
    }
  }
}
```

## POST /attendance/check-in

Record employee check-in.

### Request

```http
POST /api/attendance/check-in
Authorization: Bearer <token>
Content-Type: application/json

{
  "location": "Office - Floor 3",
  "notes": "Starting my day"
}
```

### Request Body Schema

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| location | string | No | Check-in location |
| notes | string | No | Additional notes |

### Response

```json
{
  "success": true,
  "message": "Check-in recorded successfully",
  "data": {
    "attendance": {
      "id": "att-002",
      "userId": "user-001",
      "date": "2024-02-16",
      "checkIn": "09:05:00",
      "status": "present",
      "location": "Office - Floor 3",
      "notes": "Starting my day"
    }
  }
}
```

## POST /attendance/check-out

Record employee check-out.

### Request

```http
POST /api/attendance/check-out
Authorization: Bearer <token>
Content-Type: application/json

{
  "notes": "Completed daily tasks"
}
```

### Request Body Schema

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| notes | string | No | Check-out notes |

### Response

```json
{
  "success": true,
  "message": "Check-out recorded successfully",
  "data": {
    "attendance": {
      "id": "att-002",
      "userId": "user-001",
      "date": "2024-02-16",
      "checkIn": "09:05:00",
      "checkOut": "17:45:00",
      "status": "present",
      "workingHours": 8.67,
      "overtime": 0.67,
      "notes": "Completed daily tasks"
    }
  }
}
```

## PUT /attendance/:id

Update an attendance record (HR/Manager/Admin only).

### Request

```http
PUT /api/attendance/att-001
Authorization: Bearer <token>
Content-Type: application/json

{
  "status": "late",
  "notes": "Updated by manager - late arrival approved",
  "checkIn": "09:30:00"
}
```

### Request Body Schema

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| status | string | No | Status (present, absent, late) |
| checkIn | time | No | Check-in time |
| checkOut | time | No | Check-out time |
| notes | string | No | Updated notes |
| workingHours | number | No | Manual working hours |

### Response

```json
{
  "success": true,
  "message": "Attendance record updated successfully",
  "data": {
    "attendance": {
      "id": "att-001",
      "status": "late",
      "checkIn": "09:30:00",
      "notes": "Updated by manager - late arrival approved",
      "updatedAt": "2024-02-16T10:00:00Z"
    }
  }
}
```

## DELETE /attendance/:id

Delete an attendance record (HR/Admin only).

### Request

```http
DELETE /api/attendance/att-001
Authorization: Bearer <token>
```

### Response

```json
{
  "success": true,
  "message": "Attendance record deleted successfully"
}
```

## GET /attendance/stats/overview

Get attendance statistics.

### Request

```http
GET /api/attendance/stats/overview?startDate=2024-02-01&endDate=2024-02-29
Authorization: Bearer <token>
```

### Query Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| startDate | date | Statistics start date |
| endDate | date | Statistics end date |
| userId | string | User-specific stats (optional) |

### Response

```json
{
  "success": true,
  "data": {
    "stats": {
      "totalRecords": 240,
      "present": 210,
      "absent": 15,
      "late": 15,
      "averageWorkingHours": 8.2,
      "totalWorkingHours": 1722,
      "totalOvertime": 45,
      "attendanceRate": 87.5,
      "lateRate": 6.25,
      "absentRate": 6.25,
      "byDate": [
        {
          "date": "2024-02-01",
          "present": 42,
          "absent": 2,
          "late": 1,
          "total": 45
        }
      ],
      "topPerformers": [
        {
          "userId": "user-001",
          "name": "John Doe",
          "attendanceRate": 100,
          "totalHours": 168
        }
      ]
    }
  }
}
```

## Attendance Status Types

| Status | Description | Calculation |
|--------|-------------|-------------|
| present | On time attendance | Check-in before 9:30 AM |
| late | Late arrival | Check-in after 9:30 AM |
| absent | No attendance record | No check-in for the day |

## Working Hours Calculation

- **Standard Hours**: 8 hours per day
- **Overtime**: Hours worked beyond 8 hours
- **Break Time**: Automatically deducted (default 1 hour)
- **Working Hours** = (Check-out - Check-in) - Break Time

## Error Responses

### Already Checked In (400)

```json
{
  "success": false,
  "message": "Already checked in today"
}
```

### Not Checked In (400)

```json
{
  "success": false,
  "message": "Cannot check out without checking in first"
}
```

### Invalid Time (400)

```json
{
  "success": false,
  "message": "Check-out time cannot be earlier than check-in time"
}
```

## Usage Examples

### JavaScript

```javascript
// Check-in
const checkIn = async (location = '') => {
  const response = await fetch('/api/attendance/check-in', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${token}`,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({ location })
  });
  return response.json();
};

// Check-out
const checkOut = async (notes = '') => {
  const response = await fetch('/api/attendance/check-out', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${token}`,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({ notes })
  });
  return response.json();
};

// Get attendance records
const getAttendance = async (startDate, endDate) => {
  const params = new URLSearchParams({
    startDate,
    endDate,
    page: '1',
    limit: '30'
  });
  
  const response = await fetch(`/api/attendance?${params}`, {
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

const useAttendance = () => {
  const [todayAttendance, setTodayAttendance] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    const fetchTodayAttendance = async () => {
      try {
        const today = new Date().toISOString().split('T')[0];
        const response = await fetch(`/api/attendance?date=${today}`, {
          headers: {
            'Authorization': `Bearer ${localStorage.getItem('token')}`
          }
        });
        const data = await response.json();
        setTodayAttendance(data.data.attendance[0] || null);
      } catch (error) {
        console.error('Failed to fetch attendance:', error);
      } finally {
        setLoading(false);
      }
    };

    fetchTodayAttendance();
  }, []);

  const checkIn = async (location) => {
    const response = await fetch('/api/attendance/check-in', {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${localStorage.getItem('token')}`,
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({ location })
    });
    
    if (response.ok) {
      const data = await response.json();
      setTodayAttendance(data.data.attendance);
    }
    
    return response.json();
  };

  const checkOut = async (notes) => {
    const response = await fetch('/api/attendance/check-out', {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${localStorage.getItem('token')}`,
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({ notes })
    });
    
    if (response.ok) {
      const data = await response.json();
      setTodayAttendance(data.data.attendance);
    }
    
    return response.json();
  };

  return {
    todayAttendance,
    loading,
    checkIn,
    checkOut,
    isCheckedIn: todayAttendance?.checkIn && !todayAttendance?.checkOut,
    isCheckedOut: todayAttendance?.checkIn && todayAttendance?.checkOut
  };
};
```
