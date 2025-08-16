# Leave Management API

The Leave API manages leave requests, approvals, balance tracking, and leave-related statistics.

## Endpoints Overview

| Method | Endpoint | Description | Access |
|--------|----------|-------------|---------|
| GET | `/leave` | List leave requests | All authenticated |
| GET | `/leave/:id` | Get leave request details | Owner, Approver, HR, Admin |
| POST | `/leave` | Submit leave request | All authenticated |
| PUT | `/leave/:id` | Update leave request | Owner (pending only) |
| POST | `/leave/:id/approve-reject` | Approve/reject leave | HR, Manager, Admin |
| DELETE | `/leave/:id` | Cancel leave request | Owner, Admin |
| GET | `/leave/balance/:userId` | Get leave balance | Self, HR, Admin |
| GET | `/leave/stats/overview` | Get leave statistics | HR, Admin |

## GET /leave

Retrieve leave requests with filtering and pagination.

### Request

```http
GET /api/leave?page=1&limit=10&status=pending&leaveType=casual&startDate=2024-01-01&endDate=2024-12-31
Authorization: Bearer <token>
```

### Query Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| page | number | 1 | Page number |
| limit | number | 10 | Records per page |
| status | string | - | Filter by status (pending, approved, rejected) |
| leaveType | string | - | Filter by type (casual, sick, vacation, etc.) |
| userId | string | - | Filter by user ID (HR/Admin only) |
| startDate | date | - | Filter by start date range |
| endDate | date | - | Filter by end date range |

### Response

```json
{
  "success": true,
  "data": {
    "leaveRequests": [
      {
        "id": "leave-001",
        "leaveType": "casual",
        "startDate": "2024-02-15",
        "endDate": "2024-02-16",
        "days": 2,
        "reason": "Personal work",
        "status": "pending",
        "appliedAt": "2024-02-01T10:00:00Z",
        "approvedAt": null,
        "rejectionReason": null,
        "user": {
          "id": "user-001",
          "firstName": "John",
          "lastName": "Doe",
          "employeeId": "EMP001"
        },
        "approver": {
          "id": "approver-001",
          "firstName": "Jane",
          "lastName": "Smith"
        }
      }
    ],
    "pagination": {
      "page": 1,
      "limit": 10,
      "total": 25,
      "pages": 3
    }
  }
}
```

## GET /leave/:id

Get detailed information about a specific leave request.

### Request

```http
GET /api/leave/leave-001
Authorization: Bearer <token>
```

### Response

```json
{
  "success": true,
  "data": {
    "leaveRequest": {
      "id": "leave-001",
      "userId": "user-001",
      "leaveType": "casual",
      "startDate": "2024-02-15",
      "endDate": "2024-02-16",
      "days": 2,
      "reason": "Personal work that requires my attention",
      "status": "pending",
      "appliedAt": "2024-02-01T10:00:00Z",
      "approvedAt": null,
      "approverId": "approver-001",
      "rejectionReason": null,
      "user": {
        "id": "user-001",
        "firstName": "John",
        "lastName": "Doe",
        "employeeId": "EMP001"
      },
      "approver": {
        "id": "approver-001",
        "firstName": "Jane",
        "lastName": "Smith"
      }
    }
  }
}
```

## POST /leave

Submit a new leave request.

### Request

```http
POST /api/leave
Authorization: Bearer <token>
Content-Type: application/json

{
  "leaveType": "vacation",
  "startDate": "2024-03-15",
  "endDate": "2024-03-22",
  "reason": "Family vacation to Hawaii",
  "days": 6
}
```

### Request Body Schema

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| leaveType | string | Yes | Type: casual, sick, vacation, maternity, paternity, personal |
| startDate | date | Yes | Leave start date |
| endDate | date | Yes | Leave end date |
| reason | string | Yes | Reason for leave (10-500 characters) |
| days | number | No | Number of days (auto-calculated if not provided) |

### Response

```json
{
  "success": true,
  "message": "Leave request submitted successfully",
  "data": {
    "leaveRequest": {
      "id": "leave-002",
      "leaveType": "vacation",
      "startDate": "2024-03-15",
      "endDate": "2024-03-22",
      "days": 6,
      "reason": "Family vacation to Hawaii",
      "status": "pending",
      "appliedAt": "2024-02-01T15:30:00Z",
      "user": {
        "id": "user-001",
        "firstName": "John",
        "lastName": "Doe",
        "employeeId": "EMP001"
      },
      "approver": {
        "id": "approver-001",
        "firstName": "Jane",
        "lastName": "Smith"
      }
    }
  }
}
```

## PUT /leave/:id

Update a pending leave request (owner only).

### Request

```http
PUT /api/leave/leave-001
Authorization: Bearer <token>
Content-Type: application/json

{
  "startDate": "2024-02-16",
  "endDate": "2024-02-17",
  "reason": "Updated: Personal urgent work"
}
```

### Response

```json
{
  "success": true,
  "message": "Leave request updated successfully",
  "data": {
    "leaveRequest": {
      "id": "leave-001",
      "startDate": "2024-02-16",
      "endDate": "2024-02-17",
      "days": 2,
      "reason": "Updated: Personal urgent work",
      "updatedAt": "2024-02-05T09:00:00Z"
    }
  }
}
```

## POST /leave/:id/approve-reject

Approve or reject a leave request (HR/Manager/Admin only).

### Request

```http
POST /api/leave/leave-001/approve-reject
Authorization: Bearer <token>
Content-Type: application/json

{
  "status": "approved"
}
```

### Request Body Schema (Approval)

```json
{
  "status": "approved"
}
```

### Request Body Schema (Rejection)

```json
{
  "status": "rejected",
  "rejectionReason": "Insufficient leave balance for this period"
}
```

### Response

```json
{
  "success": true,
  "message": "Leave request approved successfully",
  "data": {
    "leaveRequest": {
      "id": "leave-001",
      "status": "approved",
      "approverId": "approver-001",
      "approvedAt": "2024-02-05T14:00:00Z",
      "approver": {
        "id": "approver-001",
        "firstName": "Jane",
        "lastName": "Smith"
      }
    }
  }
}
```

## DELETE /leave/:id

Cancel a pending leave request.

### Request

```http
DELETE /api/leave/leave-001
Authorization: Bearer <token>
```

### Response

```json
{
  "success": true,
  "message": "Leave request cancelled successfully"
}
```

## GET /leave/balance/:userId

Get leave balance for a user.

### Request

```http
GET /api/leave/balance/user-001
Authorization: Bearer <token>
```

### Response

```json
{
  "success": true,
  "data": {
    "balance": {
      "casual": {
        "entitled": 12,
        "used": 3,
        "remaining": 9
      },
      "sick": {
        "entitled": 10,
        "used": 1,
        "remaining": 9
      },
      "vacation": {
        "entitled": 21,
        "used": 8,
        "remaining": 13
      },
      "maternity": {
        "entitled": 90,
        "used": 0,
        "remaining": 90
      },
      "paternity": {
        "entitled": 15,
        "used": 0,
        "remaining": 15
      },
      "personal": {
        "entitled": 5,
        "used": 2,
        "remaining": 3
      }
    },
    "year": 2024
  }
}
```

## GET /leave/stats/overview

Get organization-wide leave statistics (HR/Admin only).

### Request

```http
GET /api/leave/stats/overview
Authorization: Bearer <token>
```

### Response

```json
{
  "success": true,
  "data": {
    "stats": {
      "total": 156,
      "totalDays": 423,
      "byStatus": {
        "pending": 12,
        "approved": 128,
        "rejected": 16
      },
      "byType": {
        "casual": 98,
        "sick": 45,
        "vacation": 234,
        "maternity": 30,
        "paternity": 16,
        "personal": 0
      },
      "byMonth": [45, 38, 42, 35, 39, 28, 31, 33, 41, 38, 34, 29]
    }
  }
}
```

## Leave Types

| Type | Description | Default Entitlement |
|------|-------------|-------------------|
| casual | Casual leave | 12 days/year |
| sick | Sick leave | 10 days/year |
| vacation | Annual vacation | 21 days/year |
| maternity | Maternity leave | 90 days |
| paternity | Paternity leave | 15 days |
| personal | Personal leave | 5 days/year |

## Leave Status Flow

```
pending → approved ✓
pending → rejected ✓
approved → (cannot change)
rejected → (cannot change)
```

## Error Responses

### Access Denied (403)

```json
{
  "success": false,
  "message": "Access denied"
}
```

### Cannot Update (400)

```json
{
  "success": false,
  "message": "Cannot update approved/rejected leave request"
}
```

### Already Processed (400)

```json
{
  "success": false,
  "message": "Leave request already processed"
}
```

## Usage Examples

### JavaScript

```javascript
// Submit leave request
const submitLeave = async (leaveData) => {
  const response = await fetch('/api/leave', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${token}`,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify(leaveData)
  });
  return response.json();
};

// Approve leave request
const approveLeave = async (leaveId) => {
  const response = await fetch(`/api/leave/${leaveId}/approve-reject`, {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${token}`,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({ status: 'approved' })
  });
  return response.json();
};

// Get leave balance
const getLeaveBalance = async (userId) => {
  const response = await fetch(`/api/leave/balance/${userId}`, {
    headers: {
      'Authorization': `Bearer ${token}`
    }
  });
  return response.json();
};
```
