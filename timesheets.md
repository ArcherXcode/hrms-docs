# Timesheets API

The Timesheets API manages time tracking, project assignments, timesheet submissions, and approval workflows within the HRMS system.

## Endpoints Overview

| Method | Endpoint | Description | Access |
|--------|----------|-------------|---------|
| GET | `/timesheets` | List timesheets with filtering | All authenticated |
| GET | `/timesheets/:id` | Get timesheet details | Owner, Manager, HR, Admin |
| POST | `/timesheets` | Create new timesheet | All authenticated |
| PUT | `/timesheets/:id` | Update timesheet | Owner (if draft/pending), HR, Admin |
| DELETE | `/timesheets/:id` | Delete timesheet | Owner (if draft), Admin |
| POST | `/timesheets/:id/submit` | Submit timesheet for approval | Owner |
| POST | `/timesheets/:id/approve` | Approve timesheet | Manager, HR, Admin |
| POST | `/timesheets/:id/reject` | Reject timesheet | Manager, HR, Admin |
| GET | `/timesheets/entries` | Get time entries | All authenticated |
| POST | `/timesheets/entries` | Add time entry | All authenticated |
| PUT | `/timesheets/entries/:id` | Update time entry | Owner, HR, Admin |
| DELETE | `/timesheets/entries/:id` | Delete time entry | Owner, HR, Admin |
| GET | `/timesheets/stats/overview` | Get timesheet statistics | HR, Admin |

## GET /timesheets

Retrieve timesheets with filtering and pagination.

### Request

```http
GET /api/timesheets?page=1&limit=10&status=pending&period=2024-02&employeeId=emp-001
Authorization: Bearer <token>
```

### Query Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| page | number | 1 | Page number for pagination |
| limit | number | 10 | Number of records per page |
| status | string | - | Filter by status (draft, pending, approved, rejected) |
| employeeId | string | - | Filter by employee (Manager/HR/Admin only) |
| period | string | - | Filter by period (YYYY-MM format) |
| weekEnding | string | - | Filter by week ending date (YYYY-MM-DD) |
| projectId | string | - | Filter by project |
| dateFrom | string | - | Start date filter (YYYY-MM-DD) |
| dateTo | string | - | End date filter (YYYY-MM-DD) |
| billable | boolean | - | Filter billable/non-billable hours |

### Response

```json
{
  "success": true,
  "data": {
    "timesheets": [
      {
        "id": "ts-001",
        "employeeId": "emp-001",
        "employee": {
          "id": "emp-001",
          "firstName": "John",
          "lastName": "Doe",
          "email": "john.doe@company.com",
          "department": "Engineering"
        },
        "period": "2024-02",
        "weekEnding": "2024-02-16",
        "status": "pending",
        "totalHours": 42.5,
        "regularHours": 40.0,
        "overtimeHours": 2.5,
        "billableHours": 38.0,
        "nonBillableHours": 4.5,
        "submittedAt": "2024-02-17T18:00:00Z",
        "approver": {
          "id": "mgr-001",
          "firstName": "Sarah",
          "lastName": "Johnson",
          "role": "manager"
        },
        "projects": [
          {
            "projectId": "proj-001",
            "projectName": "Website Redesign",
            "hours": 30.0,
            "billable": true
          },
          {
            "projectId": "proj-002",
            "projectName": "Internal Training",
            "hours": 8.0,
            "billable": false
          }
        ],
        "notes": "Completed major milestone this week",
        "attachments": 1,
        "isLocked": false
      }
    ],
    "pagination": {
      "page": 1,
      "limit": 10,
      "total": 25,
      "pages": 3
    },
    "summary": {
      "totalTimesheets": 25,
      "totalHours": 986.5,
      "billableHours": 820.0,
      "overtimeHours": 45.5,
      "pendingApprovals": 8
    }
  }
}
```

## GET /timesheets/:id

Get detailed information about a specific timesheet.

### Request

```http
GET /api/timesheets/ts-001
Authorization: Bearer <token>
```

### Response

```json
{
  "success": true,
  "data": {
    "timesheet": {
      "id": "ts-001",
      "employeeId": "emp-001",
      "employee": {
        "id": "emp-001",
        "firstName": "John",
        "lastName": "Doe",
        "email": "john.doe@company.com",
        "department": "Engineering",
        "manager": {
          "id": "mgr-001",
          "firstName": "Sarah",
          "lastName": "Johnson"
        }
      },
      "period": "2024-02",
      "weekEnding": "2024-02-16",
      "startDate": "2024-02-12",
      "endDate": "2024-02-16",
      "status": "pending",
      "statusHistory": [
        {
          "status": "draft",
          "changedBy": "emp-001",
          "changedAt": "2024-02-12T09:00:00Z",
          "comment": "Initial creation"
        },
        {
          "status": "submitted",
          "changedBy": "emp-001",
          "changedAt": "2024-02-17T18:00:00Z",
          "comment": "Week completed"
        }
      ],
      "totalHours": 42.5,
      "regularHours": 40.0,
      "overtimeHours": 2.5,
      "billableHours": 38.0,
      "nonBillableHours": 4.5,
      "submittedAt": "2024-02-17T18:00:00Z",
      "createdAt": "2024-02-12T09:00:00Z",
      "updatedAt": "2024-02-17T18:00:00Z",
      "approver": {
        "id": "mgr-001",
        "firstName": "Sarah",
        "lastName": "Johnson",
        "role": "manager",
        "email": "sarah.johnson@company.com"
      },
      "entries": [
        {
          "id": "entry-001",
          "date": "2024-02-12",
          "projectId": "proj-001",
          "project": {
            "id": "proj-001",
            "name": "Website Redesign",
            "code": "WEB-001",
            "billableRate": 75.00
          },
          "taskId": "task-001",
          "task": {
            "id": "task-001",
            "name": "Frontend Development",
            "description": "React component development"
          },
          "hours": 8.0,
          "startTime": "09:00",
          "endTime": "17:00",
          "breakTime": 1.0,
          "description": "Implemented user dashboard components",
          "billable": true,
          "rate": 75.00,
          "amount": 600.00,
          "location": "office",
          "tags": ["development", "react", "frontend"]
        },
        {
          "id": "entry-002",
          "date": "2024-02-13",
          "projectId": "proj-001",
          "project": {
            "id": "proj-001",
            "name": "Website Redesign",
            "code": "WEB-001",
            "billableRate": 75.00
          },
          "taskId": "task-002",
          "task": {
            "id": "task-002",
            "name": "API Integration",
            "description": "Backend API integration"
          },
          "hours": 8.5,
          "startTime": "08:30",
          "endTime": "17:30",
          "breakTime": 0.5,
          "description": "Integrated user authentication APIs",
          "billable": true,
          "rate": 75.00,
          "amount": 637.50,
          "location": "remote",
          "tags": ["api", "integration", "backend"]
        }
      ],
      "projectSummary": [
        {
          "projectId": "proj-001",
          "projectName": "Website Redesign",
          "totalHours": 38.0,
          "billableHours": 38.0,
          "rate": 75.00,
          "totalAmount": 2850.00
        },
        {
          "projectId": "proj-002",
          "projectName": "Internal Training",
          "totalHours": 4.5,
          "billableHours": 0.0,
          "rate": 0.00,
          "totalAmount": 0.00
        }
      ],
      "dailySummary": [
        {
          "date": "2024-02-12",
          "totalHours": 8.0,
          "billableHours": 8.0,
          "projects": 1
        },
        {
          "date": "2024-02-13",
          "totalHours": 8.5,
          "billableHours": 8.5,
          "projects": 1
        }
      ],
      "notes": "Completed major milestone this week. Ready for client review.",
      "attachments": [
        {
          "id": "att-001",
          "filename": "work_screenshots.zip",
          "uploadedAt": "2024-02-17T17:45:00Z",
          "size": 512000,
          "url": "/api/documents/doc-att-001/download"
        }
      ],
      "approvalWorkflow": [
        {
          "level": 1,
          "approver": "mgr-001",
          "approverName": "Sarah Johnson",
          "status": "pending",
          "required": true
        }
      ],
      "isLocked": false,
      "lockReason": null,
      "totalAmount": 2850.00,
      "currency": "USD"
    }
  }
}
```

## POST /timesheets

Create a new timesheet.

### Request

```http
POST /api/timesheets
Authorization: Bearer <token>
Content-Type: application/json

{
  "period": "2024-02",
  "weekEnding": "2024-02-23",
  "startDate": "2024-02-19",
  "endDate": "2024-02-23",
  "notes": "Regular work week"
}
```

### Request Body Schema

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| period | string | Yes | Timesheet period (YYYY-MM format) |
| weekEnding | string | Yes | Week ending date (YYYY-MM-DD) |
| startDate | string | Yes | Week start date (YYYY-MM-DD) |
| endDate | string | Yes | Week end date (YYYY-MM-DD) |
| notes | string | No | General timesheet notes |

### Response

```json
{
  "success": true,
  "message": "Timesheet created successfully",
  "data": {
    "timesheet": {
      "id": "ts-002",
      "period": "2024-02",
      "weekEnding": "2024-02-23",
      "startDate": "2024-02-19",
      "endDate": "2024-02-23",
      "status": "draft",
      "totalHours": 0,
      "createdAt": "2024-02-19T09:00:00Z",
      "employee": {
        "id": "emp-001",
        "firstName": "John",
        "lastName": "Doe"
      }
    }
  }
}
```

## POST /timesheets/entries

Add a time entry to a timesheet.

### Request

```http
POST /api/timesheets/entries
Authorization: Bearer <token>
Content-Type: application/json

{
  "timesheetId": "ts-001",
  "date": "2024-02-14",
  "projectId": "proj-001",
  "taskId": "task-003",
  "hours": 7.5,
  "startTime": "09:00",
  "endTime": "16:30",
  "breakTime": 1.0,
  "description": "Implemented user profile page with form validation",
  "billable": true,
  "location": "office",
  "tags": ["development", "forms", "validation"]
}
```

### Request Body Schema

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| timesheetId | string | Yes | Timesheet ID |
| date | string | Yes | Work date (YYYY-MM-DD) |
| projectId | string | Yes | Project ID |
| taskId | string | No | Task ID |
| hours | number | Yes | Hours worked |
| startTime | string | No | Start time (HH:MM) |
| endTime | string | No | End time (HH:MM) |
| breakTime | number | No | Break time in hours |
| description | string | Yes | Work description |
| billable | boolean | No | Is billable (default: true) |
| location | string | No | Work location |
| tags | array | No | Work tags |

### Response

```json
{
  "success": true,
  "message": "Time entry added successfully",
  "data": {
    "entry": {
      "id": "entry-003",
      "timesheetId": "ts-001",
      "date": "2024-02-14",
      "projectId": "proj-001",
      "taskId": "task-003",
      "hours": 7.5,
      "description": "Implemented user profile page with form validation",
      "billable": true,
      "amount": 562.50,
      "createdAt": "2024-02-14T18:00:00Z"
    }
  }
}
```

## PUT /timesheets/:id

Update timesheet information.

### Request

```http
PUT /api/timesheets/ts-001
Authorization: Bearer <token>
Content-Type: application/json

{
  "notes": "Updated: Completed major milestone and additional bug fixes",
  "status": "draft"
}
```

### Request Body Schema

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| notes | string | No | Updated timesheet notes |
| status | string | No | Updated status (draft only) |

### Response

```json
{
  "success": true,
  "message": "Timesheet updated successfully",
  "data": {
    "timesheet": {
      "id": "ts-001",
      "notes": "Updated: Completed major milestone and additional bug fixes",
      "updatedAt": "2024-02-17T19:00:00Z"
    }
  }
}
```

## POST /timesheets/:id/submit

Submit timesheet for approval.

### Request

```http
POST /api/timesheets/ts-001/submit
Authorization: Bearer <token>
Content-Type: application/json

{
  "notes": "Week completed successfully. All deliverables met."
}
```

### Request Body Schema

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| notes | string | No | Submission notes |

### Response

```json
{
  "success": true,
  "message": "Timesheet submitted for approval",
  "data": {
    "timesheet": {
      "id": "ts-001",
      "status": "pending",
      "submittedAt": "2024-02-17T20:00:00Z",
      "approver": {
        "id": "mgr-001",
        "firstName": "Sarah",
        "lastName": "Johnson"
      }
    }
  }
}
```

## POST /timesheets/:id/approve

Approve a timesheet (Manager/HR/Admin only).

### Request

```http
POST /api/timesheets/ts-001/approve
Authorization: Bearer <token>
Content-Type: application/json

{
  "comment": "Approved. Hours look accurate and work is well documented.",
  "adjustments": []
}
```

### Request Body Schema

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| comment | string | No | Approval comment |
| adjustments | array | No | Hour adjustments if needed |

### Response

```json
{
  "success": true,
  "message": "Timesheet approved successfully",
  "data": {
    "timesheet": {
      "id": "ts-001",
      "status": "approved",
      "approvedBy": "mgr-001",
      "approvedAt": "2024-02-18T10:00:00Z",
      "comment": "Approved. Hours look accurate and work is well documented."
    }
  }
}
```

## POST /timesheets/:id/reject

Reject a timesheet (Manager/HR/Admin only).

### Request

```http
POST /api/timesheets/ts-001/reject
Authorization: Bearer <token>
Content-Type: application/json

{
  "reason": "Insufficient detail",
  "comment": "Please provide more detailed descriptions for the work performed on Project Alpha."
}
```

### Request Body Schema

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| reason | string | Yes | Rejection reason |
| comment | string | No | Additional rejection comment |

### Response

```json
{
  "success": true,
  "message": "Timesheet rejected",
  "data": {
    "timesheet": {
      "id": "ts-001",
      "status": "rejected",
      "rejectedBy": "mgr-001",
      "rejectedAt": "2024-02-18T10:00:00Z",
      "rejectionReason": "Insufficient detail",
      "comment": "Please provide more detailed descriptions for the work performed on Project Alpha."
    }
  }
}
```

## GET /timesheets/entries

Get time entries with filtering.

### Request

```http
GET /api/timesheets/entries?timesheetId=ts-001&date=2024-02-14&projectId=proj-001
Authorization: Bearer <token>
```

### Query Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| timesheetId | string | - | Filter by timesheet |
| date | string | - | Filter by date (YYYY-MM-DD) |
| projectId | string | - | Filter by project |
| billable | boolean | - | Filter billable entries |

### Response

```json
{
  "success": true,
  "data": {
    "entries": [
      {
        "id": "entry-001",
        "timesheetId": "ts-001",
        "date": "2024-02-14",
        "projectId": "proj-001",
        "projectName": "Website Redesign",
        "taskId": "task-003",
        "taskName": "User Profile Page",
        "hours": 7.5,
        "startTime": "09:00",
        "endTime": "16:30",
        "breakTime": 1.0,
        "description": "Implemented user profile page with form validation",
        "billable": true,
        "rate": 75.00,
        "amount": 562.50,
        "location": "office",
        "tags": ["development", "forms", "validation"]
      }
    ]
  }
}
```

## DELETE /timesheets/:id

Delete a timesheet (only if status is draft).

### Request

```http
DELETE /api/timesheets/ts-001
Authorization: Bearer <token>
```

### Response

```json
{
  "success": true,
  "message": "Timesheet deleted successfully"
}
```

## GET /timesheets/stats/overview

Get timesheet statistics and analytics.

### Request

```http
GET /api/timesheets/stats/overview?period=monthly&year=2024&month=2
Authorization: Bearer <token>
```

### Query Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| period | string | monthly | Period for stats (weekly, monthly, yearly) |
| year | number | current | Year for statistics |
| month | number | current | Month for statistics (1-12) |
| employeeId | string | - | Filter by employee |
| projectId | string | - | Filter by project |

### Response

```json
{
  "success": true,
  "data": {
    "stats": {
      "overview": {
        "totalTimesheets": 125,
        "totalHours": 4850.5,
        "billableHours": 4200.0,
        "nonBillableHours": 650.5,
        "overtimeHours": 125.5,
        "pendingApprovals": 15,
        "approvedTimesheets": 98,
        "rejectedTimesheets": 12,
        "totalRevenue": 315000.00,
        "averageHoursPerWeek": 38.8
      },
      "byStatus": {
        "draft": {
          "count": 8,
          "hours": 245.5
        },
        "pending": {
          "count": 15,
          "hours": 587.0
        },
        "approved": {
          "count": 98,
          "hours": 3918.0
        },
        "rejected": {
          "count": 4,
          "hours": 100.0
        }
      },
      "byProject": [
        {
          "projectId": "proj-001",
          "projectName": "Website Redesign",
          "totalHours": 1250.0,
          "billableHours": 1200.0,
          "employees": 5,
          "revenue": 90000.00
        },
        {
          "projectId": "proj-002",
          "projectName": "Mobile App",
          "totalHours": 980.5,
          "billableHours": 950.0,
          "employees": 4,
          "revenue": 71250.00
        }
      ],
      "byEmployee": [
        {
          "employeeId": "emp-001",
          "employeeName": "John Doe",
          "totalHours": 165.5,
          "billableHours": 152.0,
          "overtimeHours": 8.5,
          "timesheets": 4,
          "revenue": 11400.00
        }
      ],
      "weeklyTrend": [
        {
          "weekEnding": "2024-02-02",
          "totalHours": 185.0,
          "billableHours": 168.0,
          "timesheets": 5
        },
        {
          "weekEnding": "2024-02-09",
          "totalHours": 198.5,
          "billableHours": 178.0,
          "timesheets": 5
        }
      ],
      "utilizationRate": 86.8,
      "approvalRate": 92.5,
      "averageSubmissionTime": 2.3,
      "averageApprovalTime": 1.8,
      "topPerformers": [
        {
          "employeeId": "emp-001",
          "employeeName": "John Doe",
          "billableHours": 152.0,
          "efficiency": 95.2
        }
      ]
    }
  }
}
```

## Timesheet Status Flow

```
draft → submitted → pending → approved
                            ↘ rejected → draft (resubmit)
```

| Status | Description | Actions Available |
|--------|-------------|-------------------|
| draft | In progress | Edit, Delete, Add Entries, Submit |
| submitted | Submitted for approval | Edit (limited), Approve, Reject |
| pending | Under manager review | Approve, Reject |
| approved | Approved by manager | View only |
| rejected | Rejected by manager | Edit, Resubmit |

## Time Entry Categories

| Location | Description | Tracking |
|----------|-------------|----------|
| office | Office work | Standard hours |
| remote | Remote work | Standard hours |
| client_site | Client location | Travel time included |
| travel | Travel time | May have different rates |

## Billing Rates

| Employee Level | Hourly Rate | Overtime Rate |
|----------------|-------------|---------------|
| Junior | $50 | $75 |
| Mid-level | $75 | $112.50 |
| Senior | $100 | $150 |
| Lead | $125 | $187.50 |

## Error Responses

### Timesheet Not Found (404)

```json
{
  "success": false,
  "message": "Timesheet not found or access denied"
}
```

### Cannot Edit Approved Timesheet (400)

```json
{
  "success": false,
  "message": "Cannot edit approved timesheet. Status is locked."
}
```

### Invalid Date Range (400)

```json
{
  "success": false,
  "message": "Start date must be before end date"
}
```

### Overlapping Timesheet (400)

```json
{
  "success": false,
  "message": "A timesheet already exists for this period"
}
```

## Usage Examples

### JavaScript Time Tracking

```javascript
// Create new timesheet
const createTimesheet = async (period, weekEnding, startDate, endDate) => {
  const response = await fetch('/api/timesheets', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${token}`,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      period,
      weekEnding,
      startDate,
      endDate,
      notes: `Timesheet for week ending ${weekEnding}`
    })
  });
  
  return response.json();
};

// Add time entry
const addTimeEntry = async (timesheetId, entryData) => {
  const response = await fetch('/api/timesheets/entries', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${token}`,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      timesheetId,
      ...entryData
    })
  });
  
  return response.json();
};

// Submit timesheet
const submitTimesheet = async (timesheetId, notes) => {
  const response = await fetch(`/api/timesheets/${timesheetId}/submit`, {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${token}`,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({ notes })
  });
  
  return response.json();
};

// Calculate total hours for week
const calculateWeekHours = (entries) => {
  return entries.reduce((total, entry) => total + entry.hours, 0);
};

// Calculate billable vs non-billable
const calculateBillableHours = (entries) => {
  const billable = entries.filter(e => e.billable).reduce((sum, e) => sum + e.hours, 0);
  const nonBillable = entries.filter(e => !e.billable).reduce((sum, e) => sum + e.hours, 0);
  
  return { billable, nonBillable };
};
```

### React Timesheet Component

```javascript
import { useState, useEffect } from 'react';

const TimesheetWeek = ({ timesheetId, startDate, endDate, onSubmit }) => {
  const [entries, setEntries] = useState([]);
  const [projects, setProjects] = useState([]);
  const [loading, setLoading] = useState(false);

  const addEntry = async (date, projectId, hours, description) => {
    const entryData = {
      timesheetId,
      date,
      projectId,
      hours: parseFloat(hours),
      description,
      billable: true,
      location: 'office'
    };

    const response = await fetch('/api/timesheets/entries', {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${localStorage.getItem('token')}`,
        'Content-Type': 'application/json'
      },
      body: JSON.stringify(entryData)
    });

    const result = await response.json();
    
    if (result.success) {
      setEntries([...entries, result.data.entry]);
    }
  };

  const getTotalHours = () => {
    return entries.reduce((total, entry) => total + entry.hours, 0);
  };

  const getDayEntries = (date) => {
    return entries.filter(entry => entry.date === date);
  };

  const getDayHours = (date) => {
    return getDayEntries(date).reduce((total, entry) => total + entry.hours, 0);
  };

  return (
    <div className="timesheet-week">
      <div className="week-header">
        <h3>Week of {startDate} to {endDate}</h3>
        <span>Total Hours: {getTotalHours()}</span>
      </div>
      
      <div className="week-grid">
        {/* Day columns */}
        {getWeekDays(startDate, endDate).map(date => (
          <div key={date} className="day-column">
            <div className="day-header">
              <span>{formatDate(date)}</span>
              <span>{getDayHours(date)}h</span>
            </div>
            
            <div className="day-entries">
              {getDayEntries(date).map(entry => (
                <div key={entry.id} className="time-entry">
                  <span>{entry.project.name}</span>
                  <span>{entry.hours}h</span>
                  <p>{entry.description}</p>
                </div>
              ))}
            </div>
            
            <TimeEntryForm
              date={date}
              projects={projects}
              onAdd={(projectId, hours, description) => 
                addEntry(date, projectId, hours, description)
              }
            />
          </div>
        ))}
      </div>
      
      <div className="week-footer">
        <button 
          onClick={() => onSubmit(timesheetId)}
          disabled={getTotalHours() === 0}
        >
          Submit Timesheet
        </button>
      </div>
    </div>
  );
};

const TimeEntryForm = ({ date, projects, onAdd }) => {
  const [projectId, setProjectId] = useState('');
  const [hours, setHours] = useState('');
  const [description, setDescription] = useState('');

  const handleSubmit = (e) => {
    e.preventDefault();
    if (projectId && hours && description) {
      onAdd(projectId, hours, description);
      setProjectId('');
      setHours('');
      setDescription('');
    }
  };

  return (
    <form onSubmit={handleSubmit} className="entry-form">
      <select 
        value={projectId}
        onChange={(e) => setProjectId(e.target.value)}
        required
      >
        <option value="">Select Project</option>
        {projects.map(project => (
          <option key={project.id} value={project.id}>
            {project.name}
          </option>
        ))}
      </select>
      
      <input
        type="number"
        step="0.5"
        min="0"
        max="24"
        placeholder="Hours"
        value={hours}
        onChange={(e) => setHours(e.target.value)}
        required
      />
      
      <textarea
        placeholder="Description of work"
        value={description}
        onChange={(e) => setDescription(e.target.value)}
        required
      />
      
      <button type="submit">Add Entry</button>
    </form>
  );
};

// Helper functions
const getWeekDays = (startDate, endDate) => {
  const days = [];
  const current = new Date(startDate);
  const end = new Date(endDate);
  
  while (current <= end) {
    days.push(current.toISOString().split('T')[0]);
    current.setDate(current.getDate() + 1);
  }
  
  return days;
};

const formatDate = (dateStr) => {
  const date = new Date(dateStr);
  return date.toLocaleDateString('en-US', { 
    weekday: 'short', 
    month: 'short', 
    day: 'numeric' 
  });
};
```
