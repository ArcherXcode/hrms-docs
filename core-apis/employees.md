# 👥 Employees API

The Employees API provides comprehensive employee management functionality including employee profiles, onboarding, lifecycle management, and organizational hierarchy.

## Base Endpoint

```
https://api.upsurgemedia.in/api/v1/employees
```

## Overview

Employee management is central to HRMS operations, handling everything from onboarding new hires to managing employee lifecycle, performance tracking, and organizational structure.

## Employee Model

```typescript
interface Employee {
  id: string;                    // UUID
  employeeId: string;            // Unique employee identifier (e.g., EMP001)
  userId: string;                // Associated user ID
  companyId: string;             // Company ID
  departmentId?: string;         // Department ID
  managerId?: string;            // Manager ID (self-referencing)
  
  // Personal Information
  jobTitle: string;              // Job position
  hireDate: Date;                // Date of joining
  terminationDate?: Date;        // Date of leaving (if applicable)
  employmentType: EmploymentType; // Full-time, part-time, contract, etc.
  status: EmployeeStatus;        // Active, inactive, terminated
  
  // Contact Information
  workEmail?: string;            // Work email (if different from user email)
  workPhone?: string;            // Work phone number
  emergencyContact?: {
    name: string;
    relationship: string;
    phone: string;
    email?: string;
  };
  
  // Compensation
  salary?: number;               // Base salary
  currency?: string;             // Salary currency
  payFrequency?: PayFrequency;   // Monthly, bi-weekly, etc.
  
  // Work Information
  workLocation?: string;         // Office location
  reportingStructure?: string;   // Reporting hierarchy
  probationEndDate?: Date;       // End of probation period
  
  // Timestamps
  createdAt: Date;
  updatedAt: Date;
  
  // Relationships
  user?: User;                   // User account details
  company?: Company;             // Company information
  department?: Department;       // Department details
  manager?: Employee;            // Manager information
  directReports?: Employee[];    // Team members
  documents?: Document[];        // Employee documents
  attendance?: AttendanceRecord[]; // Attendance history
  leaveApplications?: LeaveApplication[]; // Leave history
}

enum EmploymentType {
  FULL_TIME = 'full_time',
  PART_TIME = 'part_time',
  CONTRACT = 'contract',
  INTERNSHIP = 'internship',
  CONSULTANT = 'consultant'
}

enum EmployeeStatus {
  ACTIVE = 'active',
  INACTIVE = 'inactive',
  TERMINATED = 'terminated',
  ON_LEAVE = 'on_leave',
  PROBATION = 'probation'
}

enum PayFrequency {
  WEEKLY = 'weekly',
  BI_WEEKLY = 'bi_weekly',
  MONTHLY = 'monthly',
  QUARTERLY = 'quarterly',
  ANNUALLY = 'annually'
}
```

## Employee Management Endpoints

### Get All Employees

Retrieve a paginated list of employees with filtering options.

**Endpoint:** `GET /employees`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `page` (number): Page number (default: 1)
- `limit` (number): Items per page (default: 10, max: 100)
- `departmentId` (string): Filter by department
- `managerId` (string): Filter by manager
- `status` (string): Filter by employee status
- `employmentType` (string): Filter by employment type
- `search` (string): Search by name, employee ID, or email
- `sortBy` (string): Sort field (name, hireDate, employeeId)
- `sortOrder` (string): Sort order (ASC, DESC)

**Response:**
```json
{
  "data": [
    {
      "id": "employee-uuid",
      "employeeId": "EMP001",
      "jobTitle": "Software Developer",
      "hireDate": "2025-01-15",
      "employmentType": "full_time",
      "status": "active",
      "salary": 75000,
      "currency": "USD",
      "user": {
        "id": "user-uuid",
        "fullName": "John Doe",
        "email": "john.doe@upsurgemedia.com"
      },
      "department": {
        "id": "dept-uuid",
        "name": "Engineering",
        "code": "ENG"
      },
      "manager": {
        "id": "manager-uuid",
        "employeeId": "EMP005",
        "user": {
          "fullName": "Jane Smith"
        }
      },
      "workLocation": "San Francisco Office",
      "createdAt": "2025-01-15T08:00:00Z"
    }
  ],
  "total": 45,
  "page": 1,
  "limit": 10,
  "totalPages": 5
}
```

**Example:**
```bash
curl -X GET "https://api.upsurgemedia.in/api/v1/employees?departmentId=eng-uuid&status=active&limit=20" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

### Get Employee by ID

Retrieve detailed information for a specific employee.

**Endpoint:** `GET /employees/{id}`

**Headers:** `Authorization: Bearer <token>`

**Response:**
```json
{
  "data": {
    "id": "employee-uuid",
    "employeeId": "EMP001",
    "jobTitle": "Senior Software Developer",
    "hireDate": "2025-01-15",
    "employmentType": "full_time",
    "status": "active",
    "salary": 85000,
    "currency": "USD",
    "payFrequency": "monthly",
    "workEmail": "john.doe@upsurgemedia.com",
    "workPhone": "+1-555-0123",
    "emergencyContact": {
      "name": "Jane Doe",
      "relationship": "Spouse",
      "phone": "+1-555-0124",
      "email": "jane.doe@email.com"
    },
    "workLocation": "San Francisco Office",
    "probationEndDate": "2025-04-15",
    "user": {
      "id": "user-uuid",
      "fullName": "John Doe",
      "email": "john.doe@upsurgemedia.com",
      "role": "employee"
    },
    "company": {
      "id": "company-uuid",
      "name": "Upsurge Media"
    },
    "department": {
      "id": "dept-uuid",
      "name": "Engineering",
      "code": "ENG"
    },
    "manager": {
      "id": "manager-uuid",
      "employeeId": "EMP005",
      "jobTitle": "Engineering Manager",
      "user": {
        "fullName": "Jane Smith",
        "email": "jane.smith@upsurgemedia.com"
      }
    },
    "directReports": [
      {
        "id": "report-uuid",
        "employeeId": "EMP010",
        "user": {
          "fullName": "Bob Wilson"
        },
        "jobTitle": "Junior Developer"
      }
    ],
    "createdAt": "2025-01-15T08:00:00Z",
    "updatedAt": "2025-09-01T10:00:00Z"
  }
}
```

### Get Current Employee Profile

Get the authenticated user's employee profile.

**Endpoint:** `GET /employees/profile`

**Headers:** `Authorization: Bearer <token>`

**Response:**
```json
{
  "data": {
    "id": "employee-uuid",
    "employeeId": "EMP001",
    "jobTitle": "Software Developer",
    "hireDate": "2025-01-15",
    "employmentType": "full_time",
    "status": "active",
    "workLocation": "San Francisco Office",
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
    },
    "company": {
      "name": "Upsurge Media",
      "logo": "https://cdn.upsurgemedia.in/logo.png"
    }
  }
}
```

### Create Employee

Create a new employee record (Admin/HR only).

**Endpoint:** `POST /employees`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "employeeId": "EMP025",
  "user": {
    "email": "alice.johnson@upsurgemedia.com",
    "fullName": "Alice Johnson",
    "role": "employee",
    "password": "temporarypass123"
  },
  "jobTitle": "Marketing Specialist",
  "departmentId": "marketing-dept-uuid",
  "managerId": "manager-uuid",
  "hireDate": "2025-09-15",
  "employmentType": "full_time",
  "salary": 65000,
  "currency": "USD",
  "payFrequency": "monthly",
  "workLocation": "San Francisco Office",
  "workPhone": "+1-555-0199",
  "emergencyContact": {
    "name": "Bob Johnson",
    "relationship": "Spouse",
    "phone": "+1-555-0200"
  }
}
```

**Response:**
```json
{
  "data": {
    "id": "new-employee-uuid",
    "employeeId": "EMP025",
    "jobTitle": "Marketing Specialist",
    "hireDate": "2025-09-15",
    "employmentType": "full_time",
    "status": "active",
    "user": {
      "id": "new-user-uuid",
      "fullName": "Alice Johnson",
      "email": "alice.johnson@upsurgemedia.com"
    },
    "department": {
      "name": "Marketing"
    },
    "createdAt": "2025-09-03T10:00:00Z"
  },
  "message": "Employee created successfully"
}
```

**Example:**
```bash
curl -X POST "https://api.upsurgemedia.in/api/v1/employees" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "employeeId": "EMP026",
    "user": {
      "email": "new.employee@upsurgemedia.com",
      "fullName": "New Employee",
      "role": "employee",
      "password": "welcome123"
    },
    "jobTitle": "Sales Representative",
    "departmentId": "sales-dept-uuid",
    "hireDate": "2025-09-10",
    "employmentType": "full_time"
  }'
```

### Update Employee

Update employee information (Admin/HR/Manager or self-update for certain fields).

**Endpoint:** `PATCH /employees/{id}`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "jobTitle": "Senior Marketing Specialist",
  "salary": 70000,
  "managerId": "new-manager-uuid",
  "workPhone": "+1-555-0250",
  "workLocation": "Remote",
  "emergencyContact": {
    "name": "Updated Contact",
    "relationship": "Parent",
    "phone": "+1-555-0300"
  }
}
```

**Response:**
```json
{
  "data": {
    "id": "employee-uuid",
    "employeeId": "EMP025",
    "jobTitle": "Senior Marketing Specialist",
    "salary": 70000,
    "workPhone": "+1-555-0250",
    "workLocation": "Remote",
    "updatedAt": "2025-09-03T10:00:00Z"
  },
  "message": "Employee updated successfully"
}
```

### Update Employee Status

Change employee status (Admin/HR only).

**Endpoint:** `PATCH /employees/{id}/status`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "status": "inactive",
  "reason": "Extended leave of absence",
  "effectiveDate": "2025-09-15"
}
```

**Response:**
```json
{
  "data": {
    "id": "employee-uuid",
    "status": "inactive",
    "statusChangeDate": "2025-09-15",
    "updatedAt": "2025-09-03T10:00:00Z"
  },
  "message": "Employee status updated successfully"
}
```

### Terminate Employee

Terminate an employee (Admin/HR only).

**Endpoint:** `POST /employees/{id}/terminate`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "terminationDate": "2025-09-30",
  "reason": "Resignation",
  "lastWorkingDay": "2025-09-29",
  "notes": "Two weeks notice provided"
}
```

**Response:**
```json
{
  "data": {
    "id": "employee-uuid",
    "status": "terminated",
    "terminationDate": "2025-09-30",
    "lastWorkingDay": "2025-09-29",
    "updatedAt": "2025-09-03T10:00:00Z"
  },
  "message": "Employee terminated successfully"
}
```

## Employee Hierarchy

### Get Direct Reports

Get employees reporting to a specific manager.

**Endpoint:** `GET /employees/{id}/direct-reports`

**Headers:** `Authorization: Bearer <token>`

**Response:**
```json
{
  "data": [
    {
      "id": "employee-uuid",
      "employeeId": "EMP010",
      "jobTitle": "Junior Developer",
      "hireDate": "2025-06-01",
      "status": "active",
      "user": {
        "fullName": "Bob Wilson",
        "email": "bob.wilson@upsurgemedia.com"
      },
      "department": {
        "name": "Engineering"
      }
    }
  ],
  "total": 3
}
```

### Get Reporting Chain

Get the complete reporting hierarchy for an employee.

**Endpoint:** `GET /employees/{id}/reporting-chain`

**Headers:** `Authorization: Bearer <token>`

**Response:**
```json
{
  "data": {
    "employee": {
      "id": "employee-uuid",
      "employeeId": "EMP001",
      "user": {
        "fullName": "John Doe"
      },
      "jobTitle": "Software Developer"
    },
    "chain": [
      {
        "level": 1,
        "employee": {
          "id": "manager-uuid",
          "employeeId": "EMP005",
          "user": {
            "fullName": "Jane Smith"
          },
          "jobTitle": "Engineering Manager"
        }
      },
      {
        "level": 2,
        "employee": {
          "id": "director-uuid",
          "employeeId": "EMP002",
          "user": {
            "fullName": "Mike Johnson"
          },
          "jobTitle": "Engineering Director"
        }
      }
    ]
  }
}
```

### Update Manager

Assign or change an employee's manager (Admin/HR only).

**Endpoint:** `PATCH /employees/{id}/manager`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "managerId": "new-manager-uuid",
  "effectiveDate": "2025-09-15"
}
```

**Response:**
```json
{
  "data": {
    "employeeId": "employee-uuid",
    "managerId": "new-manager-uuid",
    "effectiveDate": "2025-09-15",
    "updatedAt": "2025-09-03T10:00:00Z"
  },
  "message": "Manager updated successfully"
}
```

## Employee Statistics

### Get Employee Statistics

Get comprehensive employee statistics and analytics.

**Endpoint:** `GET /employees/statistics`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `departmentId` (string): Filter by department
- `period` (string): Time period (month, quarter, year)
- `includeTerminated` (boolean): Include terminated employees

**Response:**
```json
{
  "data": {
    "overview": {
      "totalEmployees": 45,
      "activeEmployees": 42,
      "inactiveEmployees": 1,
      "terminatedEmployees": 2,
      "newHires": 3,
      "departures": 1
    },
    "demographics": {
      "averageAge": 32.5,
      "averageTenure": 2.3,
      "genderDistribution": {
        "male": 26,
        "female": 18,
        "other": 1
      }
    },
    "departments": [
      {
        "department": "Engineering",
        "count": 15,
        "percentage": 33.3
      },
      {
        "department": "Marketing",
        "count": 8,
        "percentage": 17.8
      }
    ],
    "employmentTypes": {
      "full_time": 40,
      "part_time": 3,
      "contract": 2
    },
    "turnover": {
      "rate": 8.5,
      "voluntaryRate": 6.2,
      "involuntaryRate": 2.3
    },
    "compensation": {
      "averageSalary": 68500,
      "medianSalary": 65000,
      "salaryRange": {
        "min": 45000,
        "max": 120000
      }
    }
  }
}
```

### Get Department Employees

Get all employees in a specific department.

**Endpoint:** `GET /employees/by-department/{departmentId}`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `includeManager` (boolean): Include department manager info
- `status` (string): Filter by employee status

**Response:**
```json
{
  "data": {
    "department": {
      "id": "dept-uuid",
      "name": "Engineering",
      "manager": {
        "id": "manager-uuid",
        "user": {
          "fullName": "Jane Smith"
        },
        "jobTitle": "Engineering Manager"
      }
    },
    "employees": [
      {
        "id": "employee-uuid",
        "employeeId": "EMP001",
        "user": {
          "fullName": "John Doe"
        },
        "jobTitle": "Software Developer",
        "hireDate": "2025-01-15",
        "status": "active"
      }
    ],
    "total": 15
  }
}
```

## Employee Search

### Advanced Employee Search

Perform advanced search across employee data.

**Endpoint:** `GET /employees/search`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `q` (string): Search query
- `fields` (string): Comma-separated fields to search (name, email, employeeId, jobTitle)
- `departments` (string): Comma-separated department IDs
- `managers` (string): Comma-separated manager IDs
- `skills` (string): Comma-separated skills
- `dateRange` (string): Hire date range (YYYY-MM-DD,YYYY-MM-DD)

**Response:**
```json
{
  "data": [
    {
      "id": "employee-uuid",
      "employeeId": "EMP001",
      "user": {
        "fullName": "John Doe",
        "email": "john.doe@upsurgemedia.com"
      },
      "jobTitle": "Software Developer",
      "department": {
        "name": "Engineering"
      },
      "manager": {
        "user": {
          "fullName": "Jane Smith"
        }
      },
      "hireDate": "2025-01-15",
      "status": "active",
      "relevanceScore": 0.95
    }
  ],
  "total": 12,
  "query": "software developer",
  "searchTime": 0.045
}
```

## Bulk Operations

### Bulk Create Employees

Create multiple employees at once (Admin/HR only).

**Endpoint:** `POST /employees/bulk`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "employees": [
    {
      "employeeId": "EMP030",
      "user": {
        "email": "emp1@company.com",
        "fullName": "Employee One",
        "role": "employee",
        "password": "temp123"
      },
      "jobTitle": "Developer",
      "departmentId": "eng-dept-uuid",
      "hireDate": "2025-09-15"
    },
    {
      "employeeId": "EMP031",
      "user": {
        "email": "emp2@company.com",
        "fullName": "Employee Two",
        "role": "employee",
        "password": "temp123"
      },
      "jobTitle": "Designer",
      "departmentId": "design-dept-uuid",
      "hireDate": "2025-09-20"
    }
  ]
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
        "employeeId": "EMP030",
        "status": "created",
        "id": "emp1-uuid"
      },
      {
        "employeeId": "EMP031",
        "status": "created",
        "id": "emp2-uuid"
      }
    ]
  },
  "message": "Bulk employee creation completed"
}
```

### Bulk Update Employees

Update multiple employees at once (Admin/HR only).

**Endpoint:** `PATCH /employees/bulk`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "updates": [
    {
      "id": "emp1-uuid",
      "salary": 70000,
      "jobTitle": "Senior Developer"
    },
    {
      "id": "emp2-uuid",
      "departmentId": "new-dept-uuid",
      "managerId": "new-manager-uuid"
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
        "id": "emp1-uuid",
        "status": "updated"
      },
      {
        "id": "emp2-uuid",
        "status": "updated"
      }
    ]
  },
  "message": "Bulk employee update completed"
}
```

## Employee Documents

### Get Employee Documents

Get all documents associated with an employee.

**Endpoint:** `GET /employees/{id}/documents`

**Headers:** `Authorization: Bearer <token>`

**Response:**
```json
{
  "data": [
    {
      "id": "doc-uuid",
      "title": "Employment Contract",
      "type": "contract",
      "fileName": "contract_john_doe.pdf",
      "fileSize": 245760,
      "uploadedAt": "2025-01-15T08:00:00Z",
      "uploadedBy": {
        "fullName": "HR Manager"
      }
    },
    {
      "id": "doc-uuid-2",
      "title": "ID Copy",
      "type": "identification",
      "fileName": "id_copy.pdf",
      "fileSize": 156480,
      "uploadedAt": "2025-01-15T08:30:00Z"
    }
  ],
  "total": 2
}
```

### Upload Employee Document

Upload a document for an employee (Admin/HR only).

**Endpoint:** `POST /employees/{id}/documents`

**Headers:** `Authorization: Bearer <token>`

**Content-Type:** `multipart/form-data`

**Request Body:**
```
file: <document-file>
title: "Performance Review 2025"
type: "review"
description: "Annual performance review"
```

**Response:**
```json
{
  "data": {
    "id": "new-doc-uuid",
    "title": "Performance Review 2025",
    "type": "review",
    "fileName": "performance_review_2025.pdf",
    "fileSize": 189440,
    "uploadedAt": "2025-09-03T10:00:00Z"
  },
  "message": "Document uploaded successfully"
}
```

## Error Responses

### Common Error Codes

#### 400 Bad Request
```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Employee ID already exists",
    "statusCode": 400
  }
}
```

#### 403 Forbidden
```json
{
  "error": {
    "code": "FORBIDDEN",
    "message": "Insufficient permissions to access employee data",
    "statusCode": 403
  }
}
```

#### 404 Not Found
```json
{
  "error": {
    "code": "EMPLOYEE_NOT_FOUND",
    "message": "Employee with ID 'uuid' not found",
    "statusCode": 404
  }
}
```

#### 422 Validation Error
```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid hire date",
    "details": {
      "field": "hireDate",
      "value": "2025-13-01",
      "constraint": "must be a valid date"
    },
    "statusCode": 422
  }
}
```

## Integration Examples

### JavaScript/React

```javascript
class EmployeeService {
  constructor(baseURL = 'https://api.upsurgemedia.in') {
    this.api = axios.create({ baseURL });
  }

  setToken(token) {
    this.api.defaults.headers.common['Authorization'] = `Bearer ${token}`;
  }

  async getEmployees(params = {}) {
    const response = await this.api.get('/employees', { params });
    return response.data;
  }

  async getEmployee(id) {
    const response = await this.api.get(`/employees/${id}`);
    return response.data.data;
  }

  async getEmployeeProfile() {
    const response = await this.api.get('/employees/profile');
    return response.data.data;
  }

  async createEmployee(employeeData) {
    const response = await this.api.post('/employees', employeeData);
    return response.data.data;
  }

  async updateEmployee(id, updates) {
    const response = await this.api.patch(`/employees/${id}`, updates);
    return response.data.data;
  }

  async updateEmployeeStatus(id, statusData) {
    const response = await this.api.patch(`/employees/${id}/status`, statusData);
    return response.data.data;
  }

  async getDirectReports(id) {
    const response = await this.api.get(`/employees/${id}/direct-reports`);
    return response.data.data;
  }

  async searchEmployees(query, filters = {}) {
    const params = { q: query, ...filters };
    const response = await this.api.get('/employees/search', { params });
    return response.data;
  }

  async uploadDocument(employeeId, file, metadata) {
    const formData = new FormData();
    formData.append('file', file);
    Object.keys(metadata).forEach(key => {
      formData.append(key, metadata[key]);
    });

    const response = await this.api.post(`/employees/${employeeId}/documents`, formData, {
      headers: { 'Content-Type': 'multipart/form-data' }
    });
    return response.data.data;
  }
}

// Usage
const employeeService = new EmployeeService();
employeeService.setToken('your-jwt-token');

// Get all employees
const employees = await employeeService.getEmployees({
  departmentId: 'eng-uuid',
  status: 'active',
  limit: 20
});

// Create new employee
const newEmployee = await employeeService.createEmployee({
  employeeId: 'EMP040',
  user: {
    email: 'new@company.com',
    fullName: 'New Employee',
    role: 'employee',
    password: 'temp123'
  },
  jobTitle: 'Developer',
  departmentId: 'eng-uuid',
  hireDate: '2025-09-15'
});
```

### Python

```python
import requests
from typing import Dict, List, Optional

class EmployeeAPI:
    def __init__(self, base_url: str = "https://api.upsurgemedia.in"):
        self.base_url = base_url
        self.session = requests.Session()

    def set_token(self, token: str):
        self.session.headers.update({"Authorization": f"Bearer {token}"})

    def get_employees(self, **params) -> Dict:
        response = self.session.get(f"{self.base_url}/employees", params=params)
        response.raise_for_status()
        return response.json()

    def get_employee(self, employee_id: str) -> Dict:
        response = self.session.get(f"{self.base_url}/employees/{employee_id}")
        response.raise_for_status()
        return response.json()["data"]

    def create_employee(self, employee_data: Dict) -> Dict:
        response = self.session.post(
            f"{self.base_url}/employees",
            json=employee_data,
            headers={"Content-Type": "application/json"}
        )
        response.raise_for_status()
        return response.json()["data"]

    def update_employee(self, employee_id: str, updates: Dict) -> Dict:
        response = self.session.patch(
            f"{self.base_url}/employees/{employee_id}",
            json=updates,
            headers={"Content-Type": "application/json"}
        )
        response.raise_for_status()
        return response.json()["data"]

    def search_employees(self, query: str, **filters) -> Dict:
        params = {"q": query, **filters}
        response = self.session.get(f"{self.base_url}/employees/search", params=params)
        response.raise_for_status()
        return response.json()

    def get_employee_statistics(self, **params) -> Dict:
        response = self.session.get(f"{self.base_url}/employees/statistics", params=params)
        response.raise_for_status()
        return response.json()["data"]

# Usage
api = EmployeeAPI()
api.set_token("your-jwt-token")

# Get employees by department
employees = api.get_employees(
    departmentId="eng-uuid",
    status="active",
    limit=20
)
print(f"Found {employees['total']} employees")

# Search employees
search_results = api.search_employees(
    "software engineer",
    departments="eng-uuid,qa-uuid"
)
print(f"Search returned {search_results['total']} results")
```

---

The Employees API provides comprehensive employee lifecycle management with advanced search, reporting hierarchy, document management, and bulk operations for efficient HR administration.
