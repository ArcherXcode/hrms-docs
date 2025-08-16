# Onboarding API

The Onboarding API manages employee onboarding workflows, task assignments, progress tracking, and integration processes within the HRMS system.

## Endpoints Overview

| Method | Endpoint | Description | Access |
|--------|----------|-------------|---------|
| GET | `/onboarding` | List onboarding processes | HR, Admin, Manager |
| GET | `/onboarding/:id` | Get onboarding details | HR, Admin, Manager, Employee |
| POST | `/onboarding` | Create onboarding process | HR, Admin |
| PUT | `/onboarding/:id` | Update onboarding process | HR, Admin |
| DELETE | `/onboarding/:id` | Delete onboarding process | Admin |
| POST | `/onboarding/:id/start` | Start onboarding process | HR, Admin |
| POST | `/onboarding/:id/complete` | Complete onboarding | HR, Admin |
| GET | `/onboarding/:id/tasks` | Get onboarding tasks | HR, Admin, Manager, Employee |
| PUT | `/onboarding/:id/tasks/:taskId` | Update task status | HR, Admin, Manager, Employee |
| POST | `/onboarding/templates` | Create onboarding template | HR, Admin |
| GET | `/onboarding/templates` | Get onboarding templates | HR, Admin |
| GET | `/onboarding/stats/overview` | Get onboarding statistics | HR, Admin |

## GET /onboarding

Retrieve onboarding processes with filtering and pagination.

### Request

```http
GET /api/onboarding?page=1&limit=10&status=in_progress&department=Engineering
Authorization: Bearer <token>
```

### Query Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| page | number | 1 | Page number for pagination |
| limit | number | 10 | Number of records per page |
| status | string | - | Filter by status (pending, in_progress, completed, cancelled) |
| department | string | - | Filter by department |
| employeeId | string | - | Filter by employee |
| managerId | string | - | Filter by manager |
| startDate | string | - | Filter by start date |
| completionDate | string | - | Filter by completion date |

### Response

```json
{
  "success": true,
  "data": {
    "onboardingProcesses": [
      {
        "id": "onb-001",
        "employeeId": "emp-001",
        "employee": {
          "id": "emp-001",
          "firstName": "John",
          "lastName": "Doe",
          "email": "john.doe@company.com",
          "position": "Software Engineer",
          "department": "Engineering",
          "startDate": "2024-03-01"
        },
        "templateId": "tpl-001",
        "templateName": "Engineering Onboarding",
        "managerId": "mgr-001",
        "manager": {
          "id": "mgr-001",
          "firstName": "Sarah",
          "lastName": "Johnson",
          "email": "sarah.johnson@company.com"
        },
        "buddyId": "emp-005",
        "buddy": {
          "id": "emp-005",
          "firstName": "Mike",
          "lastName": "Wilson",
          "email": "mike.wilson@company.com"
        },
        "status": "in_progress",
        "progress": 65.5,
        "startedAt": "2024-03-01T09:00:00Z",
        "expectedCompletionDate": "2024-03-15",
        "actualCompletionDate": null,
        "totalTasks": 18,
        "completedTasks": 12,
        "pendingTasks": 6,
        "overdueTasks": 1,
        "priority": "high",
        "tags": ["engineering", "new-hire", "remote"],
        "notes": "New remote engineer - ensure equipment delivery",
        "createdAt": "2024-02-28T14:00:00Z",
        "updatedAt": "2024-03-10T16:30:00Z"
      }
    ],
    "pagination": {
      "page": 1,
      "limit": 10,
      "total": 25,
      "pages": 3
    },
    "summary": {
      "totalProcesses": 25,
      "inProgress": 15,
      "completed": 8,
      "overdue": 2,
      "averageCompletion": 14.5
    }
  }
}
```

## GET /onboarding/:id

Get detailed information about a specific onboarding process.

### Request

```http
GET /api/onboarding/onb-001
Authorization: Bearer <token>
```

### Response

```json
{
  "success": true,
  "data": {
    "onboarding": {
      "id": "onb-001",
      "employeeId": "emp-001",
      "employee": {
        "id": "emp-001",
        "firstName": "John",
        "lastName": "Doe",
        "email": "john.doe@company.com",
        "position": "Software Engineer",
        "department": "Engineering",
        "startDate": "2024-03-01",
        "workLocation": "Remote",
        "manager": "mgr-001"
      },
      "templateId": "tpl-001",
      "template": {
        "id": "tpl-001",
        "name": "Engineering Onboarding",
        "version": "2.1",
        "description": "Comprehensive onboarding process for engineering team members"
      },
      "managerId": "mgr-001",
      "manager": {
        "id": "mgr-001",
        "firstName": "Sarah",
        "lastName": "Johnson",
        "email": "sarah.johnson@company.com",
        "department": "Engineering"
      },
      "buddyId": "emp-005",
      "buddy": {
        "id": "emp-005",
        "firstName": "Mike",
        "lastName": "Wilson",
        "email": "mike.wilson@company.com",
        "position": "Senior Software Engineer",
        "experience": "5 years"
      },
      "hrAssignee": {
        "id": "hr-001",
        "firstName": "Jane",
        "lastName": "Smith",
        "email": "jane.smith@company.com",
        "role": "HR Specialist"
      },
      "status": "in_progress",
      "progress": 65.5,
      "startedAt": "2024-03-01T09:00:00Z",
      "expectedCompletionDate": "2024-03-15",
      "actualCompletionDate": null,
      "createdAt": "2024-02-28T14:00:00Z",
      "updatedAt": "2024-03-10T16:30:00Z",
      "phases": [
        {
          "id": "phase-001",
          "name": "Pre-boarding",
          "description": "Tasks to complete before first day",
          "order": 1,
          "status": "completed",
          "startDate": "2024-02-28",
          "endDate": "2024-03-01",
          "progress": 100.0,
          "tasks": [
            {
              "id": "task-001",
              "title": "Send welcome email",
              "description": "Send welcome email with first day information",
              "assigneeId": "hr-001",
              "assigneeName": "Jane Smith",
              "status": "completed",
              "priority": "high",
              "dueDate": "2024-02-29",
              "completedAt": "2024-02-28T15:30:00Z",
              "estimatedDuration": 30,
              "actualDuration": 20,
              "category": "communication",
              "dependencies": []
            },
            {
              "id": "task-002",
              "title": "Prepare workspace",
              "description": "Set up desk, equipment, and access cards",
              "assigneeId": "facilities-001",
              "assigneeName": "Facilities Team",
              "status": "completed",
              "priority": "high",
              "dueDate": "2024-03-01",
              "completedAt": "2024-02-29T10:00:00Z",
              "estimatedDuration": 120,
              "actualDuration": 90,
              "category": "setup",
              "dependencies": []
            }
          ]
        },
        {
          "id": "phase-002",
          "name": "First Week",
          "description": "Orientation and initial setup tasks",
          "order": 2,
          "status": "in_progress",
          "startDate": "2024-03-01",
          "endDate": "2024-03-08",
          "progress": 70.0,
          "tasks": [
            {
              "id": "task-003",
              "title": "Company orientation",
              "description": "Attend company overview and culture session",
              "assigneeId": "emp-001",
              "assigneeName": "John Doe",
              "status": "completed",
              "priority": "high",
              "dueDate": "2024-03-01",
              "completedAt": "2024-03-01T14:00:00Z",
              "estimatedDuration": 120,
              "actualDuration": 130,
              "category": "training",
              "dependencies": []
            },
            {
              "id": "task-004",
              "title": "IT setup and accounts",
              "description": "Create accounts and configure systems access",
              "assigneeId": "it-001",
              "assigneeName": "IT Support",
              "status": "in_progress",
              "priority": "high",
              "dueDate": "2024-03-02",
              "completedAt": null,
              "estimatedDuration": 180,
              "actualDuration": null,
              "category": "setup",
              "dependencies": ["task-002"]
            }
          ]
        }
      ],
      "documents": [
        {
          "id": "doc-001",
          "title": "Employee Handbook",
          "description": "Company policies and procedures",
          "url": "/documents/employee-handbook.pdf",
          "required": true,
          "acknowledged": true,
          "acknowledgedAt": "2024-03-01T11:00:00Z"
        },
        {
          "id": "doc-002",
          "title": "Engineering Guidelines",
          "description": "Development practices and standards",
          "url": "/documents/engineering-guidelines.pdf",
          "required": true,
          "acknowledged": false,
          "acknowledgedAt": null
        }
      ],
      "equipment": [
        {
          "id": "eq-001",
          "name": "MacBook Pro 16\"",
          "category": "laptop",
          "serialNumber": "MB-2024-001",
          "status": "assigned",
          "assignedAt": "2024-03-01T09:00:00Z"
        },
        {
          "id": "eq-002",
          "name": "External Monitor",
          "category": "monitor",
          "serialNumber": "MON-2024-012",
          "status": "pending",
          "assignedAt": null
        }
      ],
      "checklist": [
        {
          "id": "check-001",
          "item": "Office tour completed",
          "completed": true,
          "completedAt": "2024-03-01T10:30:00Z",
          "completedBy": "hr-001"
        },
        {
          "id": "check-002",
          "item": "ID photo taken",
          "completed": true,
          "completedAt": "2024-03-01T11:00:00Z",
          "completedBy": "hr-001"
        },
        {
          "id": "check-003",
          "item": "Emergency contact information provided",
          "completed": false,
          "completedAt": null,
          "completedBy": null
        }
      ],
      "feedback": [
        {
          "id": "fb-001",
          "fromId": "emp-001",
          "fromName": "John Doe",
          "type": "progress_update",
          "message": "Everything is going well, but still waiting for monitor delivery",
          "createdAt": "2024-03-05T16:00:00Z"
        }
      ],
      "totalTasks": 18,
      "completedTasks": 12,
      "pendingTasks": 5,
      "overdueTasks": 1,
      "completionRate": 66.7,
      "averageTaskDuration": 95,
      "timeToComplete": null,
      "satisfactionScore": null,
      "notes": "Remote employee - prioritize virtual introductions and ensure equipment delivery tracking"
    }
  }
}
```

## POST /onboarding

Create a new onboarding process for an employee.

### Request

```http
POST /api/onboarding
Authorization: Bearer <token>
Content-Type: application/json

{
  "employeeId": "emp-002",
  "templateId": "tpl-001",
  "managerId": "mgr-001",
  "buddyId": "emp-005",
  "startDate": "2024-03-15",
  "expectedCompletionDate": "2024-03-29",
  "priority": "high",
  "customizations": {
    "skipTasks": ["task-010"],
    "additionalTasks": [
      {
        "title": "Security clearance setup",
        "description": "Complete additional security requirements",
        "assigneeId": "security-001",
        "dueDate": "2024-03-18",
        "category": "compliance"
      }
    ]
  },
  "notes": "New senior engineer with security clearance requirements"
}
```

### Request Body Schema

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| employeeId | string | Yes | Employee ID for onboarding |
| templateId | string | Yes | Onboarding template ID |
| managerId | string | Yes | Assigned manager ID |
| buddyId | string | No | Onboarding buddy ID |
| startDate | string | Yes | Onboarding start date (YYYY-MM-DD) |
| expectedCompletionDate | string | No | Expected completion date |
| priority | string | No | Priority level (low, medium, high, urgent) |
| customizations | object | No | Template customizations |
| notes | string | No | Additional notes |

### Response

```json
{
  "success": true,
  "message": "Onboarding process created successfully",
  "data": {
    "onboarding": {
      "id": "onb-002",
      "employeeId": "emp-002",
      "templateId": "tpl-001",
      "managerId": "mgr-001",
      "buddyId": "emp-005",
      "status": "pending",
      "startDate": "2024-03-15",
      "expectedCompletionDate": "2024-03-29",
      "priority": "high",
      "totalTasks": 19,
      "createdAt": "2024-03-10T14:30:00Z"
    }
  }
}
```

## POST /onboarding/:id/start

Start an onboarding process.

### Request

```http
POST /api/onboarding/onb-001/start
Authorization: Bearer <token>
Content-Type: application/json

{
  "actualStartDate": "2024-03-01",
  "notes": "Employee started on schedule"
}
```

### Request Body Schema

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| actualStartDate | string | No | Actual start date if different from planned |
| notes | string | No | Start notes |

### Response

```json
{
  "success": true,
  "message": "Onboarding process started successfully",
  "data": {
    "onboarding": {
      "id": "onb-001",
      "status": "in_progress",
      "startedAt": "2024-03-01T09:00:00Z",
      "progress": 0.0
    }
  }
}
```

## PUT /onboarding/:id/tasks/:taskId

Update the status of a specific onboarding task.

### Request

```http
PUT /api/onboarding/onb-001/tasks/task-004
Authorization: Bearer <token>
Content-Type: application/json

{
  "status": "completed",
  "completedAt": "2024-03-02T15:30:00Z",
  "actualDuration": 150,
  "notes": "All systems configured successfully",
  "attachments": [
    {
      "filename": "system_setup_confirmation.pdf",
      "url": "/documents/doc-system-setup-001"
    }
  ]
}
```

### Request Body Schema

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| status | string | Yes | Task status (pending, in_progress, completed, skipped) |
| completedAt | string | No | Completion timestamp |
| actualDuration | number | No | Actual duration in minutes |
| notes | string | No | Task completion notes |
| attachments | array | No | Task attachments |

### Response

```json
{
  "success": true,
  "message": "Task updated successfully",
  "data": {
    "task": {
      "id": "task-004",
      "status": "completed",
      "completedAt": "2024-03-02T15:30:00Z",
      "actualDuration": 150,
      "progress": 100.0
    },
    "onboardingProgress": {
      "overallProgress": 72.2,
      "completedTasks": 13,
      "pendingTasks": 5
    }
  }
}
```

## GET /onboarding/templates

Get available onboarding templates.

### Request

```http
GET /api/onboarding/templates?department=Engineering&active=true
Authorization: Bearer <token>
```

### Query Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| department | string | - | Filter by department |
| active | boolean | - | Filter active templates |
| category | string | - | Filter by category |

### Response

```json
{
  "success": true,
  "data": {
    "templates": [
      {
        "id": "tpl-001",
        "name": "Engineering Onboarding",
        "description": "Comprehensive onboarding process for engineering team members",
        "department": "Engineering",
        "category": "technical",
        "version": "2.1",
        "isActive": true,
        "estimatedDuration": 14,
        "totalTasks": 18,
        "phases": 4,
        "createdAt": "2024-01-01T00:00:00Z",
        "updatedAt": "2024-02-15T10:00:00Z",
        "createdBy": "hr-001",
        "usageCount": 25,
        "successRate": 95.2,
        "averageCompletionTime": 12.5,
        "tags": ["engineering", "technical", "development"]
      },
      {
        "id": "tpl-002",
        "name": "Sales Onboarding",
        "description": "Sales team onboarding with CRM training and client introduction",
        "department": "Sales",
        "category": "sales",
        "version": "1.8",
        "isActive": true,
        "estimatedDuration": 10,
        "totalTasks": 14,
        "phases": 3,
        "createdAt": "2024-01-01T00:00:00Z",
        "updatedAt": "2024-01-20T14:00:00Z",
        "createdBy": "hr-001",
        "usageCount": 18,
        "successRate": 92.8,
        "averageCompletionTime": 9.2,
        "tags": ["sales", "crm", "client-facing"]
      }
    ]
  }
}
```

## POST /onboarding/templates

Create a new onboarding template.

### Request

```http
POST /api/onboarding/templates
Authorization: Bearer <token>
Content-Type: application/json

{
  "name": "Marketing Onboarding",
  "description": "Comprehensive onboarding for marketing team members",
  "department": "Marketing",
  "category": "creative",
  "estimatedDuration": 12,
  "phases": [
    {
      "name": "Pre-boarding",
      "description": "Preparation before first day",
      "order": 1,
      "estimatedDuration": 2,
      "tasks": [
        {
          "title": "Send welcome package",
          "description": "Send welcome email and company swag",
          "assigneeRole": "hr",
          "priority": "high",
          "estimatedDuration": 30,
          "category": "communication",
          "daysFromStart": -1
        }
      ]
    },
    {
      "name": "First Week",
      "description": "Initial orientation and setup",
      "order": 2,
      "estimatedDuration": 5,
      "tasks": [
        {
          "title": "Company orientation",
          "description": "Attend company overview session",
          "assigneeRole": "employee",
          "priority": "high",
          "estimatedDuration": 120,
          "category": "training",
          "daysFromStart": 0
        },
        {
          "title": "Marketing tools training",
          "description": "Learn marketing automation tools",
          "assigneeRole": "employee",
          "priority": "medium",
          "estimatedDuration": 240,
          "category": "training",
          "daysFromStart": 2,
          "dependencies": ["company-orientation"]
        }
      ]
    }
  ],
  "requiredDocuments": [
    {
      "title": "Employee Handbook",
      "url": "/documents/employee-handbook.pdf",
      "required": true
    },
    {
      "title": "Marketing Guidelines",
      "url": "/documents/marketing-guidelines.pdf",
      "required": true
    }
  ],
  "defaultEquipment": [
    {
      "category": "laptop",
      "specifications": "MacBook Air 13\"",
      "required": true
    },
    {
      "category": "software",
      "specifications": "Adobe Creative Suite",
      "required": true
    }
  ],
  "tags": ["marketing", "creative", "tools"]
}
```

### Response

```json
{
  "success": true,
  "message": "Onboarding template created successfully",
  "data": {
    "template": {
      "id": "tpl-003",
      "name": "Marketing Onboarding",
      "version": "1.0",
      "isActive": true,
      "totalTasks": 15,
      "phases": 2,
      "createdAt": "2024-03-10T16:00:00Z"
    }
  }
}
```

## POST /onboarding/:id/complete

Complete an onboarding process.

### Request

```http
POST /api/onboarding/onb-001/complete
Authorization: Bearer <token>
Content-Type: application/json

{
  "completionDate": "2024-03-14",
  "satisfactionScore": 4.5,
  "feedback": "Great onboarding experience. Very well organized and informative.",
  "recommendations": "Consider adding more technical deep-dive sessions"
}
```

### Request Body Schema

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| completionDate | string | No | Actual completion date |
| satisfactionScore | number | No | Satisfaction rating (1-5) |
| feedback | string | No | Employee feedback |
| recommendations | string | No | Improvement recommendations |

### Response

```json
{
  "success": true,
  "message": "Onboarding process completed successfully",
  "data": {
    "onboarding": {
      "id": "onb-001",
      "status": "completed",
      "completedAt": "2024-03-14T17:00:00Z",
      "actualDuration": 13,
      "satisfactionScore": 4.5,
      "completionRate": 100.0
    }
  }
}
```

## DELETE /onboarding/:id

Delete an onboarding process (Admin only).

### Request

```http
DELETE /api/onboarding/onb-001
Authorization: Bearer <token>
```

### Response

```json
{
  "success": true,
  "message": "Onboarding process deleted successfully"
}
```

## GET /onboarding/stats/overview

Get onboarding statistics and analytics.

### Request

```http
GET /api/onboarding/stats/overview?period=quarterly&year=2024&quarter=1
Authorization: Bearer <token>
```

### Response

```json
{
  "success": true,
  "data": {
    "stats": {
      "overview": {
        "totalOnboardings": 45,
        "completedOnboardings": 38,
        "inProgressOnboardings": 6,
        "cancelledOnboardings": 1,
        "completionRate": 84.4,
        "averageCompletionTime": 13.2,
        "onTimeCompletionRate": 78.9,
        "averageSatisfactionScore": 4.3
      },
      "byDepartment": [
        {
          "department": "Engineering",
          "total": 18,
          "completed": 16,
          "completionRate": 88.9,
          "avgCompletionTime": 14.5,
          "avgSatisfactionScore": 4.4
        },
        {
          "department": "Sales",
          "total": 12,
          "completed": 10,
          "completionRate": 83.3,
          "avgCompletionTime": 10.8,
          "avgSatisfactionScore": 4.2
        },
        {
          "department": "Marketing",
          "total": 8,
          "completed": 7,
          "completionRate": 87.5,
          "avgCompletionTime": 12.1,
          "avgSatisfactionScore": 4.5
        }
      ],
      "taskAnalytics": {
        "mostCompletedTasks": [
          {
            "taskName": "Company orientation",
            "completionRate": 98.9,
            "avgDuration": 125
          },
          {
            "taskName": "IT setup",
            "completionRate": 95.6,
            "avgDuration": 165
          }
        ],
        "problematicTasks": [
          {
            "taskName": "Security training completion",
            "completionRate": 67.8,
            "avgDuration": 280,
            "delayReasons": ["Scheduling conflicts", "System access issues"]
          }
        ]
      },
      "satisfactionTrends": [
        {
          "month": "2024-01",
          "averageScore": 4.2,
          "responseRate": 85.7
        },
        {
          "month": "2024-02",
          "averageScore": 4.4,
          "responseRate": 92.1
        },
        {
          "month": "2024-03",
          "averageScore": 4.5,
          "responseRate": 89.5
        }
      ],
      "templatePerformance": [
        {
          "templateId": "tpl-001",
          "templateName": "Engineering Onboarding",
          "usage": 18,
          "completionRate": 88.9,
          "avgSatisfaction": 4.4,
          "avgDuration": 14.5
        },
        {
          "templateId": "tpl-002",
          "templateName": "Sales Onboarding",
          "usage": 12,
          "completionRate": 83.3,
          "avgSatisfaction": 4.2,
          "avgDuration": 10.8
        }
      ],
      "commonFeedback": [
        {
          "theme": "Equipment delivery delays",
          "mentions": 12,
          "sentiment": "negative",
          "impact": "medium"
        },
        {
          "theme": "Excellent buddy system",
          "mentions": 28,
          "sentiment": "positive",
          "impact": "high"
        },
        {
          "theme": "Information overload in first week",
          "mentions": 8,
          "sentiment": "negative",
          "impact": "low"
        }
      ],
      "timeToProductivity": {
        "average": 21.5,
        "byDepartment": {
          "Engineering": 25.2,
          "Sales": 18.3,
          "Marketing": 19.7,
          "HR": 16.5
        }
      }
    }
  }
}
```

## Onboarding Process Status

| Status | Description | Actions Available |
|--------|-------------|-------------------|
| pending | Created but not started | Edit, Start, Delete |
| in_progress | Currently active | Update tasks, Monitor progress |
| completed | Successfully completed | View only, Generate reports |
| cancelled | Cancelled before completion | View only |
| on_hold | Temporarily paused | Resume, Cancel |

## Task Categories

| Category | Description | Typical Duration |
|----------|-------------|------------------|
| communication | Welcome messages, introductions | 15-60 minutes |
| setup | Equipment, accounts, workspace | 30-180 minutes |
| training | Courses, orientations, skills | 60-480 minutes |
| documentation | Reading, acknowledging policies | 30-120 minutes |
| compliance | Legal, security, certifications | 60-240 minutes |
| social | Team meetings, buddy sessions | 30-90 minutes |

## Task Priorities

| Priority | Description | SLA |
|----------|-------------|-----|
| urgent | Critical path items | Same day |
| high | Important for progress | 1-2 days |
| medium | Standard tasks | 3-5 days |
| low | Nice-to-have items | 1-2 weeks |

## Error Responses

### Onboarding Not Found (404)

```json
{
  "success": false,
  "message": "Onboarding process not found"
}
```

### Template Not Found (404)

```json
{
  "success": false,
  "message": "Onboarding template not found"
}
```

### Invalid Status Transition (400)

```json
{
  "success": false,
  "message": "Cannot transition from completed to in_progress status"
}
```

### Task Dependencies Not Met (400)

```json
{
  "success": false,
  "message": "Cannot complete task. Dependencies not satisfied: ['company-orientation']"
}
```

## Usage Examples

### JavaScript Onboarding Management

```javascript
// Create onboarding process
const createOnboarding = async (employeeId, templateId, managerId, buddyId, startDate) => {
  const response = await fetch('/api/onboarding', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${token}`,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      employeeId,
      templateId,
      managerId,
      buddyId,
      startDate,
      priority: 'high',
      notes: 'New hire onboarding process'
    })
  });
  
  return response.json();
};

// Start onboarding process
const startOnboarding = async (onboardingId, startDate) => {
  const response = await fetch(`/api/onboarding/${onboardingId}/start`, {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${token}`,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      actualStartDate: startDate,
      notes: 'Employee started as scheduled'
    })
  });
  
  return response.json();
};

// Update task status
const updateTaskStatus = async (onboardingId, taskId, status, notes) => {
  const response = await fetch(`/api/onboarding/${onboardingId}/tasks/${taskId}`, {
    method: 'PUT',
    headers: {
      'Authorization': `Bearer ${token}`,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      status,
      completedAt: status === 'completed' ? new Date().toISOString() : null,
      notes
    })
  });
  
  return response.json();
};

// Get onboarding progress
const getOnboardingProgress = async (onboardingId) => {
  const response = await fetch(`/api/onboarding/${onboardingId}`, {
    headers: {
      'Authorization': `Bearer ${token}`
    }
  });
  
  return response.json();
};
```

### React Onboarding Dashboard

```javascript
import { useState, useEffect } from 'react';

const OnboardingDashboard = () => {
  const [onboardings, setOnboardings] = useState([]);
  const [selectedOnboarding, setSelectedOnboarding] = useState(null);
  const [loading, setLoading] = useState(false);

  useEffect(() => {
    loadOnboardings();
  }, []);

  const loadOnboardings = async () => {
    setLoading(true);
    try {
      const response = await fetch('/api/onboarding?status=in_progress', {
        headers: {
          'Authorization': `Bearer ${localStorage.getItem('token')}`
        }
      });
      
      const result = await response.json();
      
      if (result.success) {
        setOnboardings(result.data.onboardingProcesses);
      }
    } catch (error) {
      console.error('Error loading onboardings:', error);
    } finally {
      setLoading(false);
    }
  };

  const updateTaskStatus = async (taskId, status) => {
    if (!selectedOnboarding) return;
    
    try {
      const response = await fetch(
        `/api/onboarding/${selectedOnboarding.id}/tasks/${taskId}`,
        {
          method: 'PUT',
          headers: {
            'Authorization': `Bearer ${localStorage.getItem('token')}`,
            'Content-Type': 'application/json'
          },
          body: JSON.stringify({
            status,
            completedAt: status === 'completed' ? new Date().toISOString() : null
          })
        }
      );

      const result = await response.json();
      
      if (result.success) {
        // Reload the selected onboarding to get updated progress
        loadOnboardingDetails(selectedOnboarding.id);
        // Update the list as well
        loadOnboardings();
      }
    } catch (error) {
      console.error('Error updating task:', error);
    }
  };

  const loadOnboardingDetails = async (onboardingId) => {
    try {
      const response = await fetch(`/api/onboarding/${onboardingId}`, {
        headers: {
          'Authorization': `Bearer ${localStorage.getItem('token')}`
        }
      });
      
      const result = await response.json();
      
      if (result.success) {
        setSelectedOnboarding(result.data.onboarding);
      }
    } catch (error) {
      console.error('Error loading onboarding details:', error);
    }
  };

  const getStatusColor = (status) => {
    const colors = {
      'pending': 'text-yellow-600 bg-yellow-100',
      'in_progress': 'text-blue-600 bg-blue-100',
      'completed': 'text-green-600 bg-green-100',
      'overdue': 'text-red-600 bg-red-100'
    };
    return colors[status] || colors.pending;
  };

  const getPriorityColor = (priority) => {
    const colors = {
      'urgent': 'text-red-600',
      'high': 'text-orange-600',
      'medium': 'text-blue-600',
      'low': 'text-gray-600'
    };
    return colors[priority] || colors.medium;
  };

  return (
    <div className="onboarding-dashboard">
      <div className="dashboard-header">
        <h1>Onboarding Dashboard</h1>
        <div className="stats-summary">
          <div className="stat-card">
            <span className="stat-value">{onboardings.length}</span>
            <span className="stat-label">Active Onboardings</span>
          </div>
        </div>
      </div>

      <div className="dashboard-content">
        <div className="onboarding-list">
          <h2>Active Onboardings</h2>
          {loading ? (
            <div className="loading">Loading onboardings...</div>
          ) : (
            <div className="onboardings-grid">
              {onboardings.map(onboarding => (
                <div
                  key={onboarding.id}
                  className={`onboarding-card cursor-pointer ${
                    selectedOnboarding?.id === onboarding.id ? 'selected' : ''
                  }`}
                  onClick={() => loadOnboardingDetails(onboarding.id)}
                >
                  <div className="card-header">
                    <h3>
                      {onboarding.employee.firstName} {onboarding.employee.lastName}
                    </h3>
                    <span className={`status-badge ${getStatusColor(onboarding.status)}`}>
                      {onboarding.status.replace('_', ' ')}
                    </span>
                  </div>
                  
                  <div className="card-content">
                    <p className="position">
                      {onboarding.employee.position} - {onboarding.employee.department}
                    </p>
                    
                    <div className="progress-section">
                      <div className="progress-bar">
                        <div 
                          className="progress-fill"
                          style={{ width: `${onboarding.progress}%` }}
                        />
                      </div>
                      <span className="progress-text">
                        {onboarding.progress.toFixed(1)}% Complete
                      </span>
                    </div>
                    
                    <div className="task-summary">
                      <span>
                        {onboarding.completedTasks}/{onboarding.totalTasks} tasks
                      </span>
                      {onboarding.overdueTasks > 0 && (
                        <span className="overdue-indicator">
                          {onboarding.overdueTasks} overdue
                        </span>
                      )}
                    </div>
                    
                    <div className="meta-info">
                      <span className={getPriorityColor(onboarding.priority)}>
                        {onboarding.priority} priority
                      </span>
                      <span>
                        Due: {new Date(onboarding.expectedCompletionDate).toLocaleDateString()}
                      </span>
                    </div>
                  </div>
                </div>
              ))}
            </div>
          )}
        </div>

        {selectedOnboarding && (
          <div className="onboarding-details">
            <div className="details-header">
              <h2>
                {selectedOnboarding.employee.firstName} {selectedOnboarding.employee.lastName} - Onboarding
              </h2>
              <div className="header-actions">
                <button 
                  className="btn btn-primary"
                  onClick={() => {/* Complete onboarding logic */}}
                  disabled={selectedOnboarding.status === 'completed'}
                >
                  Mark Complete
                </button>
              </div>
            </div>

            <div className="phases-container">
              {selectedOnboarding.phases.map(phase => (
                <div key={phase.id} className="phase-section">
                  <div className="phase-header">
                    <h3>{phase.name}</h3>
                    <span className={`status-badge ${getStatusColor(phase.status)}`}>
                      {phase.status.replace('_', ' ')}
                    </span>
                    <div className="phase-progress">
                      <div className="progress-bar">
                        <div 
                          className="progress-fill"
                          style={{ width: `${phase.progress}%` }}
                        />
                      </div>
                      <span>{phase.progress.toFixed(0)}%</span>
                    </div>
                  </div>
                  
                  <p className="phase-description">{phase.description}</p>
                  
                  <div className="tasks-list">
                    {phase.tasks.map(task => (
                      <div key={task.id} className="task-item">
                        <div className="task-checkbox">
                          <input
                            type="checkbox"
                            checked={task.status === 'completed'}
                            onChange={(e) => 
                              updateTaskStatus(
                                task.id, 
                                e.target.checked ? 'completed' : 'pending'
                              )
                            }
                          />
                        </div>
                        
                        <div className="task-content">
                          <h4 className={task.status === 'completed' ? 'completed' : ''}>
                            {task.title}
                          </h4>
                          <p>{task.description}</p>
                          
                          <div className="task-meta">
                            <span>Assigned to: {task.assigneeName}</span>
                            <span className={getPriorityColor(task.priority)}>
                              {task.priority} priority
                            </span>
                            <span>Due: {new Date(task.dueDate).toLocaleDateString()}</span>
                            {task.completedAt && (
                              <span className="completed-at">
                                Completed: {new Date(task.completedAt).toLocaleDateString()}
                              </span>
                            )}
                          </div>
                        </div>
                      </div>
                    ))}
                  </div>
                </div>
              ))}
            </div>

            <div className="additional-sections">
              <div className="documents-section">
                <h3>Required Documents</h3>
                <div className="documents-list">
                  {selectedOnboarding.documents.map(doc => (
                    <div key={doc.id} className="document-item">
                      <span className={doc.acknowledged ? 'acknowledged' : 'pending'}>
                        {doc.title}
                      </span>
                      <span className="acknowledgment-status">
                        {doc.acknowledged ? '✅ Acknowledged' : '⏳ Pending'}
                      </span>
                    </div>
                  ))}
                </div>
              </div>

              <div className="equipment-section">
                <h3>Equipment Assignment</h3>
                <div className="equipment-list">
                  {selectedOnboarding.equipment.map(eq => (
                    <div key={eq.id} className="equipment-item">
                      <span>{eq.name}</span>
                      <span className={`status ${eq.status}`}>
                        {eq.status}
                      </span>
                    </div>
                  ))}
                </div>
              </div>
            </div>
          </div>
        )}
      </div>
    </div>
  );
};
```

The Onboarding API provides comprehensive employee onboarding management with template-based workflows, task tracking, progress monitoring, and analytics. It supports customizable onboarding processes tailored to different departments and roles while maintaining consistency and compliance.
