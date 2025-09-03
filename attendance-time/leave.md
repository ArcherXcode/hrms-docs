# 🏖️ Leave Management API

The Leave Management API provides comprehensive leave application, approval workflow, balance tracking, and leave policy management functionality.

## Base Endpoint

```
https://api.upsurgemedia.in/leave
```

## Overview

The Leave Management API handles all aspects of employee leave management including leave applications, approval workflows, balance calculations, leave types configuration, and comprehensive leave analytics.

## Leave Models

```typescript
interface LeaveApplication {
  id: string;                    // UUID
  employeeId: string;            // Employee ID
  leaveTypeId: string;           // Leave type ID
  companyId: string;             // Company ID
  
  // Leave Details
  startDate: Date;               // Leave start date
  endDate: Date;                 // Leave end date
  totalDays: number;             // Total leave days (calculated)
  reason: string;                // Leave reason/description
  
  // Status and Workflow
  status: LeaveStatus;           // Pending, approved, rejected, cancelled
  appliedAt: Date;              // Application timestamp
  respondedAt?: Date;           // Response timestamp
  
  // Approval Workflow
  approvedBy?: string;          // Approver user ID
  rejectedBy?: string;          // Rejector user ID
  approvalNotes?: string;       // Approval/rejection notes
  
  // Additional Information
  isEmergency: boolean;         // Emergency leave flag
  contactNumber?: string;       // Emergency contact during leave
  handoverNotes?: string;       // Work handover information
  attachments?: string[];       // Supporting documents
  
  // Timestamps
  createdAt: Date;
  updatedAt: Date;
  
  // Relationships
  employee?: Employee;          // Employee details
  leaveType?: LeaveType;        // Leave type details
  approver?: User;              // Approver details
}

interface LeaveType {
  id: string;                   // UUID
  companyId: string;            // Company ID
  name: string;                 // Leave type name (e.g., Annual, Sick)
  code: string;                 // Leave type code (e.g., AL, SL)
  description?: string;         // Leave type description
  
  // Policy Configuration
  maxDaysPerYear: number;       // Maximum days per year
  maxConsecutiveDays?: number;  // Maximum consecutive days
  carryForwardDays?: number;    // Days that can be carried forward
  isCarryForwardEnabled: boolean; // Carry forward allowed
  
  // Application Rules
  minAdvanceNotice?: number;    // Minimum advance notice in days
  maxAdvanceApplication?: number; // Maximum advance application in days
  requiresApproval: boolean;    // Requires manager approval
  requiresDocument: boolean;    // Requires supporting documents
  
  // Availability
  isActive: boolean;            // Active status
  applicableRoles?: string[];   // Applicable user roles
  
  // Timestamps
  createdAt: Date;
  updatedAt: Date;
}

interface LeaveBalance {
  id: string;                   // UUID
  employeeId: string;           // Employee ID
  leaveTypeId: string;          // Leave type ID
  year: number;                 // Calendar year
  
  // Balance Information
  allocatedDays: number;        // Total allocated days
  usedDays: number;             // Used days
  pendingDays: number;          // Pending approval days
  availableDays: number;        // Available days (calculated)
  carriedForwardDays: number;   // Carried forward from previous year
  
  // Timestamps
  createdAt: Date;
  updatedAt: Date;
  
  // Relationships
  employee?: Employee;
  leaveType?: LeaveType;
}

enum LeaveStatus {
  PENDING = 'pending',
  APPROVED = 'approved',
  REJECTED = 'rejected',
  CANCELLED = 'cancelled',
  EXPIRED = 'expired'
}
```

## Leave Application Endpoints

### Apply for Leave

Submit a new leave application.

**Endpoint:** `POST /leave`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "leaveTypeId": "leave-type-uuid",
  "startDate": "2025-09-15",
  "endDate": "2025-09-17",
  "reason": "Family vacation - pre-planned trip to visit relatives",
  "isEmergency": false,
  "contactNumber": "+1-555-0123",
  "handoverNotes": "Will delegate tasks to John and complete pending reviews before departure"
}
```

**Response:**
```json
{
  "data": {
    "id": "leave-app-uuid",
    "employeeId": "employee-uuid",
    "leaveTypeId": "leave-type-uuid",
    "startDate": "2025-09-15",
    "endDate": "2025-09-17",
    "totalDays": 3,
    "reason": "Family vacation - pre-planned trip to visit relatives",
    "status": "pending",
    "isEmergency": false,
    "appliedAt": "2025-09-03T10:00:00Z",
    "employee": {
      "employeeId": "EMP001",
      "user": {
        "fullName": "John Doe"
      }
    },
    "leaveType": {
      "name": "Annual Leave",
      "code": "AL"
    }
  },
  "message": "Leave application submitted successfully"
}
```

**Example:**
```bash
curl -X POST "https://api.upsurgemedia.in/leave" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "leaveTypeId": "annual-leave-uuid",
    "startDate": "2025-09-20",
    "endDate": "2025-09-22",
    "reason": "Personal work - house relocation"
  }'
```

### Get Leave Applications

Retrieve leave applications with filtering and pagination.

**Endpoint:** `GET /leave`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `employeeId` (string): Filter by employee
- `leaveTypeId` (string): Filter by leave type
- `status` (string): Filter by status
- `startDate` (string): Filter by start date (YYYY-MM-DD)
- `endDate` (string): Filter by end date (YYYY-MM-DD)
- `year` (number): Filter by year
- `departmentId` (string): Filter by department (Manager/HR only)
- `page` (number): Page number
- `limit` (number): Items per page

**Response:**
```json
{
  "data": [
    {
      "id": "leave-app-uuid",
      "startDate": "2025-09-15",
      "endDate": "2025-09-17",
      "totalDays": 3,
      "reason": "Family vacation",
      "status": "approved",
      "appliedAt": "2025-09-03T10:00:00Z",
      "approvedAt": "2025-09-04T14:30:00Z",
      "employee": {
        "employeeId": "EMP001",
        "user": {
          "fullName": "John Doe"
        },
        "department": {
          "name": "Engineering"
        }
      },
      "leaveType": {
        "name": "Annual Leave",
        "code": "AL"
      },
      "approver": {
        "fullName": "Jane Smith"
      }
    }
  ],
  "total": 25,
  "page": 1,
  "limit": 10,
  "summary": {
    "totalApplications": 25,
    "pendingApplications": 3,
    "approvedApplications": 20,
    "rejectedApplications": 2
  }
}
```

### Get Leave Application by ID

Retrieve detailed information for a specific leave application.

**Endpoint:** `GET /leave/{id}`

**Headers:** `Authorization: Bearer <token>`

**Response:**
```json
{
  "data": {
    "id": "leave-app-uuid",
    "startDate": "2025-09-15",
    "endDate": "2025-09-17",
    "totalDays": 3,
    "reason": "Family vacation - pre-planned trip to visit relatives",
    "status": "approved",
    "isEmergency": false,
    "contactNumber": "+1-555-0123",
    "handoverNotes": "Will delegate tasks to John and complete pending reviews before departure",
    "appliedAt": "2025-09-03T10:00:00Z",
    "approvedAt": "2025-09-04T14:30:00Z",
    "approvalNotes": "Approved. Ensure handover is completed before departure.",
    "employee": {
      "employeeId": "EMP001",
      "user": {
        "fullName": "John Doe",
        "email": "john.doe@upsurgemedia.com"
      },
      "department": {
        "name": "Engineering"
      },
      "manager": {
        "user": {
          "fullName": "Jane Smith"
        }
      }
    },
    "leaveType": {
      "name": "Annual Leave",
      "code": "AL",
      "maxDaysPerYear": 20
    },
    "approver": {
      "fullName": "Jane Smith",
      "email": "jane.smith@upsurgemedia.com"
    },
    "attachments": []
  }
}
```

### Update Leave Application

Update a pending leave application (Employee can update own applications).

**Endpoint:** `PATCH /leave/{id}`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "startDate": "2025-09-16",
  "endDate": "2025-09-18",
  "reason": "Updated: Family vacation - extended by one day",
  "contactNumber": "+1-555-0199"
}
```

**Response:**
```json
{
  "data": {
    "id": "leave-app-uuid",
    "startDate": "2025-09-16",
    "endDate": "2025-09-18",
    "totalDays": 3,
    "reason": "Updated: Family vacation - extended by one day",
    "status": "pending",
    "updatedAt": "2025-09-03T11:00:00Z"
  },
  "message": "Leave application updated successfully"
}
```

### Cancel Leave Application

Cancel a leave application (Employee can cancel own applications).

**Endpoint:** `POST /leave/{id}/cancel`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "reason": "Plans changed due to work commitments"
}
```

**Response:**
```json
{
  "data": {
    "id": "leave-app-uuid",
    "status": "cancelled",
    "cancelledAt": "2025-09-03T12:00:00Z",
    "cancellationReason": "Plans changed due to work commitments"
  },
  "message": "Leave application cancelled successfully"
}
```

## Leave Approval Endpoints

### Get Pending Approvals

Get leave applications pending approval (Manager/HR only).

**Endpoint:** `GET /leave/pending-approvals`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `departmentId` (string): Filter by department
- `employeeId` (string): Filter by employee
- `urgent` (boolean): Filter urgent/emergency leaves

**Response:**
```json
{
  "data": [
    {
      "id": "leave-app-uuid",
      "startDate": "2025-09-10",
      "endDate": "2025-09-12",
      "totalDays": 3,
      "reason": "Medical appointment and recovery",
      "isEmergency": true,
      "appliedAt": "2025-09-05T16:30:00Z",
      "employee": {
        "employeeId": "EMP002",
        "user": {
          "fullName": "Alice Johnson"
        },
        "department": {
          "name": "Marketing"
        }
      },
      "leaveType": {
        "name": "Sick Leave",
        "code": "SL"
      },
      "daysUntilStart": 7,
      "availableBalance": 8
    }
  ],
  "total": 5,
  "urgent": 2
}
```

### Approve Leave Application

Approve a leave application (Manager/HR only).

**Endpoint:** `POST /leave/{id}/approve`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "approvalNotes": "Approved. Please ensure proper handover before departure.",
  "conditions": "Complete project documentation before leaving"
}
```

**Response:**
```json
{
  "data": {
    "id": "leave-app-uuid",
    "status": "approved",
    "approvedBy": "manager-user-uuid",
    "approvedAt": "2025-09-03T14:00:00Z",
    "approvalNotes": "Approved. Please ensure proper handover before departure."
  },
  "message": "Leave application approved successfully"
}
```

### Reject Leave Application

Reject a leave application (Manager/HR only).

**Endpoint:** `POST /leave/{id}/reject`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "rejectionReason": "Leave conflicts with critical project deadline. Please consider alternative dates.",
  "suggestedAlternatives": "Consider applying for dates after September 25th"
}
```

**Response:**
```json
{
  "data": {
    "id": "leave-app-uuid",
    "status": "rejected",
    "rejectedBy": "manager-user-uuid",
    "rejectedAt": "2025-09-03T14:00:00Z",
    "rejectionReason": "Leave conflicts with critical project deadline. Please consider alternative dates."
  },
  "message": "Leave application rejected"
}
```

## Leave Balance Management

### Get Leave Balance

Get leave balance for an employee.

**Endpoint:** `GET /leave/balance`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `employeeId` (string): Employee ID (optional, defaults to current user)
- `year` (number): Year (optional, defaults to current year)
- `leaveTypeId` (string): Specific leave type (optional)

**Response:**
```json
{
  "data": [
    {
      "leaveType": {
        "id": "annual-leave-uuid",
        "name": "Annual Leave",
        "code": "AL",
        "maxDaysPerYear": 20
      },
      "allocatedDays": 20,
      "usedDays": 8,
      "pendingDays": 3,
      "availableDays": 9,
      "carriedForwardDays": 2,
      "year": 2025,
      "lastUpdated": "2025-09-03T10:00:00Z"
    },
    {
      "leaveType": {
        "id": "sick-leave-uuid",
        "name": "Sick Leave",
        "code": "SL",
        "maxDaysPerYear": 12
      },
      "allocatedDays": 12,
      "usedDays": 2,
      "pendingDays": 0,
      "availableDays": 10,
      "carriedForwardDays": 0,
      "year": 2025
    }
  ],
  "employee": {
    "employeeId": "EMP001",
    "user": {
      "fullName": "John Doe"
    }
  },
  "year": 2025
}
```

### Update Leave Balance

Update leave balance for an employee (HR only).

**Endpoint:** `PATCH /leave/balance/{employeeId}`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "leaveTypeId": "annual-leave-uuid",
  "year": 2025,
  "allocatedDays": 25,
  "reason": "Promotion bonus - additional 5 days",
  "adjustmentType": "increase"
}
```

**Response:**
```json
{
  "data": {
    "leaveTypeId": "annual-leave-uuid",
    "employeeId": "employee-uuid",
    "year": 2025,
    "previousAllocatedDays": 20,
    "newAllocatedDays": 25,
    "adjustmentDays": 5,
    "reason": "Promotion bonus - additional 5 days",
    "updatedAt": "2025-09-03T15:00:00Z"
  },
  "message": "Leave balance updated successfully"
}
```

### Get Team Leave Balance

Get leave balance summary for a team/department (Manager/HR only).

**Endpoint:** `GET /leave/balance/team/{departmentId}`

**Headers:** `Authorization: Bearer <token>`

**Response:**
```json
{
  "data": {
    "department": {
      "id": "dept-uuid",
      "name": "Engineering"
    },
    "summary": {
      "totalEmployees": 15,
      "totalAllocatedDays": 300,
      "totalUsedDays": 125,
      "totalAvailableDays": 175,
      "utilizationRate": 41.7
    },
    "employees": [
      {
        "employee": {
          "employeeId": "EMP001",
          "user": {
            "fullName": "John Doe"
          }
        },
        "balances": [
          {
            "leaveType": "Annual Leave",
            "allocatedDays": 20,
            "usedDays": 8,
            "availableDays": 12
          }
        ],
        "totalAvailable": 22,
        "utilizationRate": 35.0
      }
    ]
  }
}
```

## Leave Types Management

### Get Leave Types

Get all available leave types for the company.

**Endpoint:** `GET /leave/types`

**Headers:** `Authorization: Bearer <token>`

**Response:**
```json
{
  "data": [
    {
      "id": "annual-leave-uuid",
      "name": "Annual Leave",
      "code": "AL",
      "description": "Yearly vacation leave for rest and recreation",
      "maxDaysPerYear": 20,
      "maxConsecutiveDays": 10,
      "carryForwardDays": 5,
      "isCarryForwardEnabled": true,
      "minAdvanceNotice": 7,
      "maxAdvanceApplication": 365,
      "requiresApproval": true,
      "requiresDocument": false,
      "isActive": true,
      "applicableRoles": ["employee", "manager", "hr"]
    },
    {
      "id": "sick-leave-uuid",
      "name": "Sick Leave",
      "code": "SL",
      "description": "Leave for medical reasons and health recovery",
      "maxDaysPerYear": 12,
      "maxConsecutiveDays": 30,
      "carryForwardDays": 0,
      "isCarryForwardEnabled": false,
      "minAdvanceNotice": 0,
      "requiresApproval": false,
      "requiresDocument": true,
      "isActive": true
    }
  ]
}
```

### Create Leave Type

Create a new leave type (Admin/HR only).

**Endpoint:** `POST /leave/types`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "name": "Maternity Leave",
  "code": "ML",
  "description": "Leave for maternity and child care",
  "maxDaysPerYear": 90,
  "maxConsecutiveDays": 90,
  "carryForwardDays": 0,
  "isCarryForwardEnabled": false,
  "minAdvanceNotice": 30,
  "maxAdvanceApplication": 180,
  "requiresApproval": true,
  "requiresDocument": true,
  "applicableRoles": ["employee"]
}
```

**Response:**
```json
{
  "data": {
    "id": "maternity-leave-uuid",
    "name": "Maternity Leave",
    "code": "ML",
    "description": "Leave for maternity and child care",
    "maxDaysPerYear": 90,
    "isActive": true,
    "createdAt": "2025-09-03T16:00:00Z"
  },
  "message": "Leave type created successfully"
}
```

### Update Leave Type

Update leave type configuration (Admin/HR only).

**Endpoint:** `PATCH /leave/types/{id}`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "maxDaysPerYear": 25,
  "carryForwardDays": 7,
  "minAdvanceNotice": 5
}
```

**Response:**
```json
{
  "data": {
    "id": "annual-leave-uuid",
    "maxDaysPerYear": 25,
    "carryForwardDays": 7,
    "minAdvanceNotice": 5,
    "updatedAt": "2025-09-03T16:30:00Z"
  },
  "message": "Leave type updated successfully"
}
```

## Leave Analytics

### Get Leave Statistics

Get comprehensive leave analytics and statistics.

**Endpoint:** `GET /leave/statistics`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `period` (string): Time period (month, quarter, year)
- `departmentId` (string): Filter by department
- `leaveTypeId` (string): Filter by leave type
- `year` (number): Specific year

**Response:**
```json
{
  "data": {
    "overview": {
      "totalApplications": 245,
      "approvedApplications": 220,
      "rejectedApplications": 15,
      "pendingApplications": 10,
      "approvalRate": 89.8,
      "averageResponseTime": 1.8
    },
    "leaveUtilization": {
      "totalAllocatedDays": 1200,
      "totalUsedDays": 890,
      "utilizationRate": 74.2,
      "topUtilizedTypes": [
        {
          "leaveType": "Annual Leave",
          "usedDays": 580,
          "utilizationRate": 85.3
        },
        {
          "leaveType": "Sick Leave",
          "usedDays": 210,
          "utilizationRate": 52.5
        }
      ]
    },
    "departmentWise": [
      {
        "department": "Engineering",
        "totalApplications": 85,
        "approvalRate": 92.9,
        "utilizationRate": 78.5,
        "averageLeaveDays": 12.8
      }
    ],
    "trends": {
      "monthly": [
        {
          "month": "2025-01",
          "applications": 18,
          "approvals": 16,
          "avgResponseTime": 1.5
        }
      ],
      "seasonality": {
        "peakMonths": ["December", "January", "July"],
        "lowMonths": ["February", "September"]
      }
    },
    "emergencyLeaves": {
      "total": 25,
      "percentage": 10.2,
      "averageNotice": 0.8
    }
  }
}
```

### Get Leave Calendar

Get leave calendar showing upcoming leaves.

**Endpoint:** `GET /leave/calendar`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `startDate` (string): Calendar start date
- `endDate` (string): Calendar end date
- `departmentId` (string): Filter by department
- `view` (string): View type (month, week, day)

**Response:**
```json
{
  "data": {
    "period": {
      "startDate": "2025-09-01",
      "endDate": "2025-09-30"
    },
    "leaves": [
      {
        "id": "leave-app-uuid",
        "employee": {
          "employeeId": "EMP001",
          "user": {
            "fullName": "John Doe"
          },
          "department": {
            "name": "Engineering"
          }
        },
        "leaveType": {
          "name": "Annual Leave",
          "code": "AL"
        },
        "startDate": "2025-09-15",
        "endDate": "2025-09-17",
        "totalDays": 3,
        "status": "approved"
      }
    ],
    "summary": {
      "totalLeaveDays": 45,
      "employeesOnLeave": 12,
      "conflictingLeaves": 2,
      "departmentCoverage": {
        "Engineering": 87.5,
        "Marketing": 92.3
      }
    }
  }
}
```

## Error Responses

### Common Error Codes

#### 400 Bad Request
```json
{
  "error": {
    "code": "INSUFFICIENT_BALANCE",
    "message": "Insufficient leave balance for this request",
    "details": {
      "requested": 5,
      "available": 3,
      "leaveType": "Annual Leave"
    },
    "statusCode": 400
  }
}
```

#### 422 Validation Error
```json
{
  "error": {
    "code": "INVALID_DATE_RANGE",
    "message": "Start date cannot be after end date",
    "details": {
      "startDate": "2025-09-20",
      "endDate": "2025-09-15"
    },
    "statusCode": 422
  }
}
```

#### 403 Forbidden
```json
{
  "error": {
    "code": "INSUFFICIENT_NOTICE",
    "message": "Minimum 7 days advance notice required for this leave type",
    "details": {
      "requiredNotice": 7,
      "providedNotice": 3
    },
    "statusCode": 403
  }
}
```

## Integration Examples

### JavaScript/React

```javascript
class LeaveService {
  constructor(baseURL = 'https://api.upsurgemedia.in') {
    this.api = axios.create({ baseURL });
  }

  setToken(token) {
    this.api.defaults.headers.common['Authorization'] = `Bearer ${token}`;
  }

  async applyLeave(leaveData) {
    const response = await this.api.post('/leave', leaveData);
    return response.data.data;
  }

  async getLeaveApplications(params = {}) {
    const response = await this.api.get('/leave', { params });
    return response.data;
  }

  async getLeaveBalance(employeeId = null, year = null) {
    const params = {};
    if (employeeId) params.employeeId = employeeId;
    if (year) params.year = year;
    
    const response = await this.api.get('/leave/balance', { params });
    return response.data.data;
  }

  async approveLeave(leaveId, approvalData) {
    const response = await this.api.post(`/leave/${leaveId}/approve`, approvalData);
    return response.data.data;
  }

  async rejectLeave(leaveId, rejectionData) {
    const response = await this.api.post(`/leave/${leaveId}/reject`, rejectionData);
    return response.data.data;
  }

  async getPendingApprovals() {
    const response = await this.api.get('/leave/pending-approvals');
    return response.data.data;
  }

  async getLeaveTypes() {
    const response = await this.api.get('/leave/types');
    return response.data.data;
  }

  async getLeaveCalendar(startDate, endDate, departmentId = null) {
    const params = { startDate, endDate };
    if (departmentId) params.departmentId = departmentId;
    
    const response = await this.api.get('/leave/calendar', { params });
    return response.data.data;
  }

  async getLeaveStatistics(params = {}) {
    const response = await this.api.get('/leave/statistics', { params });
    return response.data.data;
  }
}

// Usage
const leaveService = new LeaveService();
leaveService.setToken('your-jwt-token');

// Apply for leave
const leaveApplication = await leaveService.applyLeave({
  leaveTypeId: 'annual-leave-uuid',
  startDate: '2025-09-20',
  endDate: '2025-09-22',
  reason: 'Family vacation'
});

// Get leave balance
const balance = await leaveService.getLeaveBalance();
console.log('Annual leave available:', balance[0].availableDays);

// Get pending approvals (for managers)
const pendingApprovals = await leaveService.getPendingApprovals();
console.log(`${pendingApprovals.length} leaves pending approval`);
```

### Python

```python
import requests
from datetime import datetime, date
from typing import Dict, List, Optional

class LeaveAPI:
    def __init__(self, base_url: str = "https://api.upsurgemedia.in"):
        self.base_url = base_url
        self.session = requests.Session()

    def set_token(self, token: str):
        self.session.headers.update({"Authorization": f"Bearer {token}"})

    def apply_leave(self, leave_data: Dict) -> Dict:
        response = self.session.post(
            f"{self.base_url}/leave",
            json=leave_data
        )
        response.raise_for_status()
        return response.json()["data"]

    def get_leave_applications(self, **params) -> Dict:
        response = self.session.get(
            f"{self.base_url}/leave",
            params=params
        )
        response.raise_for_status()
        return response.json()

    def get_leave_balance(self, employee_id: Optional[str] = None, 
                         year: Optional[int] = None) -> List[Dict]:
        params = {}
        if employee_id:
            params["employeeId"] = employee_id
        if year:
            params["year"] = year
            
        response = self.session.get(
            f"{self.base_url}/leave/balance",
            params=params
        )
        response.raise_for_status()
        return response.json()["data"]

    def approve_leave(self, leave_id: str, approval_data: Dict) -> Dict:
        response = self.session.post(
            f"{self.base_url}/leave/{leave_id}/approve",
            json=approval_data
        )
        response.raise_for_status()
        return response.json()["data"]

    def get_pending_approvals(self, **params) -> List[Dict]:
        response = self.session.get(
            f"{self.base_url}/leave/pending-approvals",
            params=params
        )
        response.raise_for_status()
        return response.json()["data"]

    def get_leave_calendar(self, start_date: str, end_date: str, 
                          department_id: Optional[str] = None) -> Dict:
        params = {"startDate": start_date, "endDate": end_date}
        if department_id:
            params["departmentId"] = department_id
            
        response = self.session.get(
            f"{self.base_url}/leave/calendar",
            params=params
        )
        response.raise_for_status()
        return response.json()["data"]

# Usage
api = LeaveAPI()
api.set_token("your-jwt-token")

# Apply for leave
leave_app = api.apply_leave({
    "leaveTypeId": "annual-leave-uuid",
    "startDate": "2025-09-25",
    "endDate": "2025-09-27",
    "reason": "Personal work - house hunting"
})
print(f"Leave application ID: {leave_app['id']}")

# Check leave balance
balance = api.get_leave_balance()
for leave_type in balance:
    print(f"{leave_type['leaveType']['name']}: {leave_type['availableDays']} days available")

# Get team calendar (for managers)
calendar = api.get_leave_calendar("2025-09-01", "2025-09-30")
print(f"Total employees on leave in September: {calendar['summary']['employeesOnLeave']}")
```

---

The Leave Management API provides comprehensive leave administration with flexible approval workflows, detailed balance tracking, and powerful analytics for effective leave policy management.
