# 🏢 Departments API

The Departments API provides comprehensive organizational structure management including department creation, hierarchy management, and team organization functionality.

## Base Endpoint

```
https://api.upsurgemedia.in/api/v1/departments
```

## Overview

The Departments API enables organizations to structure their workforce into logical departments, manage reporting hierarchies, and organize teams effectively for better administration and reporting.

## Department Model

```typescript
interface Department {
  id: string;                    // UUID
  companyId: string;             // Company ID
  name: string;                  // Department name
  code: string;                  // Department code (e.g., ENG, HR, MKT)
  description?: string;          // Department description
  
  // Hierarchy
  parentDepartmentId?: string;   // Parent department ID
  level: number;                 // Hierarchy level (0 = top level)
  
  // Management
  managerId?: string;            // Department manager employee ID
  
  // Budget and Operations
  budget?: number;               // Department budget
  budgetCurrency?: string;       // Budget currency
  costCenter?: string;           // Cost center code
  
  // Location
  location?: string;             // Department location
  floor?: string;                // Office floor
  
  // Settings
  isActive: boolean;             // Department status
  canHaveSubDepartments: boolean; // Allow sub-departments
  
  // Timestamps
  createdAt: Date;
  updatedAt: Date;
  
  // Relationships
  company?: Company;             // Company details
  parentDepartment?: Department; // Parent department
  subDepartments?: Department[]; // Child departments
  manager?: Employee;            // Department manager
  employees?: Employee[];        // Department employees
  
  // Computed Fields
  employeeCount?: number;        // Total employees
  directEmployeeCount?: number;  // Direct employees (excluding sub-departments)
}
```

## Department Management Endpoints

### Get All Departments

Retrieve departments with optional hierarchy and filtering.

**Endpoint:** `GET /departments`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `includeHierarchy` (boolean): Include parent/child relationships
- `includeEmployeeCount` (boolean): Include employee counts
- `parentId` (string): Filter by parent department
- `level` (number): Filter by hierarchy level
- `managerId` (string): Filter by manager
- `isActive` (boolean): Filter by active status
- `search` (string): Search by name or code

**Response:**
```json
{
  "data": [
    {
      "id": "dept-uuid-1",
      "name": "Engineering",
      "code": "ENG",
      "description": "Software development and technology teams",
      "level": 0,
      "managerId": "manager-uuid-1",
      "budget": 500000,
      "budgetCurrency": "USD",
      "location": "Building A, Floors 3-4",
      "isActive": true,
      "canHaveSubDepartments": true,
      "manager": {
        "id": "manager-uuid-1",
        "employeeId": "EMP005",
        "user": {
          "fullName": "Jane Smith",
          "email": "jane.smith@upsurgemedia.com"
        },
        "jobTitle": "Engineering Director"
      },
      "employeeCount": 25,
      "directEmployeeCount": 5,
      "subDepartments": [
        {
          "id": "subdept-uuid-1",
          "name": "Frontend Development",
          "code": "ENG-FE",
          "level": 1,
          "employeeCount": 8
        },
        {
          "id": "subdept-uuid-2",
          "name": "Backend Development",
          "code": "ENG-BE",
          "level": 1,
          "employeeCount": 12
        }
      ],
      "createdAt": "2025-01-01T00:00:00Z"
    }
  ],
  "total": 8,
  "hierarchy": {
    "maxLevel": 2,
    "totalDepartments": 8,
    "activeDepartments": 7
  }
}
```

**Example:**
```bash
curl -X GET "https://api.upsurgemedia.in/api/v1/departments?includeHierarchy=true&includeEmployeeCount=true" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

### Get Department by ID

Retrieve detailed information for a specific department.

**Endpoint:** `GET /departments/{id}`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `includeEmployees` (boolean): Include department employees
- `includeSubDepartments` (boolean): Include sub-departments
- `includeStatistics` (boolean): Include department statistics

**Response:**
```json
{
  "data": {
    "id": "dept-uuid-1",
    "name": "Engineering",
    "code": "ENG",
    "description": "Software development and technology teams",
    "level": 0,
    "budget": 500000,
    "budgetCurrency": "USD",
    "costCenter": "CC-ENG-001",
    "location": "Building A, Floors 3-4",
    "floor": "3-4",
    "isActive": true,
    "canHaveSubDepartments": true,
    "manager": {
      "id": "manager-uuid-1",
      "employeeId": "EMP005",
      "user": {
        "fullName": "Jane Smith",
        "email": "jane.smith@upsurgemedia.com"
      },
      "jobTitle": "Engineering Director",
      "hireDate": "2024-01-15"
    },
    "employees": [
      {
        "id": "emp-uuid-1",
        "employeeId": "EMP001",
        "user": {
          "fullName": "John Doe",
          "email": "john.doe@upsurgemedia.com"
        },
        "jobTitle": "Senior Software Engineer",
        "hireDate": "2025-01-15",
        "status": "active"
      }
    ],
    "subDepartments": [
      {
        "id": "subdept-uuid-1",
        "name": "Frontend Development",
        "code": "ENG-FE",
        "level": 1,
        "employeeCount": 8,
        "manager": {
          "user": {
            "fullName": "Alice Johnson"
          }
        }
      }
    ],
    "statistics": {
      "totalEmployees": 25,
      "directEmployees": 5,
      "activeEmployees": 24,
      "avgTenure": 2.3,
      "avgSalary": 85000,
      "budgetUtilization": 87.5,
      "attendanceRate": 94.2
    },
    "createdAt": "2025-01-01T00:00:00Z",
    "updatedAt": "2025-08-15T10:00:00Z"
  }
}
```

### Create Department

Create a new department (Admin/HR only).

**Endpoint:** `POST /departments`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "name": "Data Science",
  "code": "DS",
  "description": "Data analytics and machine learning teams",
  "parentDepartmentId": "tech-dept-uuid",
  "managerId": "manager-uuid-3",
  "budget": 300000,
  "budgetCurrency": "USD",
  "costCenter": "CC-DS-001",
  "location": "Building B, Floor 2",
  "floor": "2",
  "canHaveSubDepartments": true
}
```

**Response:**
```json
{
  "data": {
    "id": "new-dept-uuid",
    "name": "Data Science",
    "code": "DS",
    "description": "Data analytics and machine learning teams",
    "parentDepartmentId": "tech-dept-uuid",
    "level": 1,
    "managerId": "manager-uuid-3",
    "budget": 300000,
    "budgetCurrency": "USD",
    "isActive": true,
    "canHaveSubDepartments": true,
    "employeeCount": 0,
    "createdAt": "2025-09-03T10:00:00Z"
  },
  "message": "Department created successfully"
}
```

**Example:**
```bash
curl -X POST "https://api.upsurgemedia.in/api/v1/departments" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Quality Assurance",
    "code": "QA",
    "description": "Software testing and quality control",
    "parentDepartmentId": "eng-dept-uuid",
    "managerId": "qa-manager-uuid"
  }'
```

### Update Department

Update department information (Admin/HR only).

**Endpoint:** `PATCH /departments/{id}`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "name": "Engineering & Technology",
  "description": "Software development, infrastructure, and technology innovation teams",
  "budget": 600000,
  "location": "Building A, Floors 3-5",
  "floor": "3-5",
  "managerId": "new-manager-uuid"
}
```

**Response:**
```json
{
  "data": {
    "id": "dept-uuid-1",
    "name": "Engineering & Technology",
    "description": "Software development, infrastructure, and technology innovation teams",
    "budget": 600000,
    "location": "Building A, Floors 3-5",
    "floor": "3-5",
    "managerId": "new-manager-uuid",
    "updatedAt": "2025-09-03T11:00:00Z"
  },
  "message": "Department updated successfully"
}
```

### Delete Department

Soft delete a department (Admin only).

**Endpoint:** `DELETE /departments/{id}`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `transferEmployeesTo` (string): Department ID to transfer employees to
- `force` (boolean): Force delete even with employees (moves to company level)

**Response:**
```json
{
  "data": {
    "deletedDepartmentId": "dept-uuid",
    "transferredEmployees": 5,
    "transferredToDepartment": "target-dept-uuid",
    "deletedAt": "2025-09-03T12:00:00Z"
  },
  "message": "Department deleted successfully"
}
```

## Department Hierarchy

### Get Department Hierarchy

Get the complete organizational hierarchy.

**Endpoint:** `GET /departments/hierarchy`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `format` (string): Response format (tree, flat)
- `includeEmployeeCounts` (boolean): Include employee counts
- `maxLevel` (number): Maximum hierarchy level to include

**Response:**
```json
{
  "data": {
    "hierarchy": [
      {
        "id": "dept-uuid-1",
        "name": "Engineering",
        "code": "ENG",
        "level": 0,
        "employeeCount": 25,
        "manager": {
          "user": {
            "fullName": "Jane Smith"
          }
        },
        "children": [
          {
            "id": "subdept-uuid-1",
            "name": "Frontend Development",
            "code": "ENG-FE",
            "level": 1,
            "employeeCount": 8,
            "manager": {
              "user": {
                "fullName": "Alice Johnson"
              }
            },
            "children": []
          },
          {
            "id": "subdept-uuid-2",
            "name": "Backend Development",
            "code": "ENG-BE",
            "level": 1,
            "employeeCount": 12,
            "children": [
              {
                "id": "subdept-uuid-3",
                "name": "API Development",
                "code": "ENG-BE-API",
                "level": 2,
                "employeeCount": 6,
                "children": []
              }
            ]
          }
        ]
      }
    ],
    "summary": {
      "totalDepartments": 8,
      "maxLevel": 2,
      "totalEmployees": 45,
      "departmentsByLevel": {
        "0": 3,
        "1": 4,
        "2": 1
      }
    }
  }
}
```

### Move Department

Move a department to a different parent (Admin/HR only).

**Endpoint:** `PATCH /departments/{id}/move`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "newParentId": "target-parent-uuid",
  "reason": "Organizational restructuring - aligning teams better"
}
```

**Response:**
```json
{
  "data": {
    "departmentId": "dept-uuid",
    "previousParentId": "old-parent-uuid",
    "newParentId": "target-parent-uuid",
    "previousLevel": 1,
    "newLevel": 2,
    "affectedSubDepartments": 3,
    "movedAt": "2025-09-03T13:00:00Z"
  },
  "message": "Department moved successfully"
}
```

## Department Employees

### Get Department Employees

Get all employees in a department.

**Endpoint:** `GET /departments/{id}/employees`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `includeSubDepartments` (boolean): Include employees from sub-departments
- `status` (string): Filter by employee status
- `role` (string): Filter by user role
- `page` (number): Page number
- `limit` (number): Items per page

**Response:**
```json
{
  "data": [
    {
      "id": "emp-uuid-1",
      "employeeId": "EMP001",
      "jobTitle": "Senior Software Engineer",
      "hireDate": "2025-01-15",
      "status": "active",
      "user": {
        "fullName": "John Doe",
        "email": "john.doe@upsurgemedia.com",
        "role": "employee"
      },
      "manager": {
        "user": {
          "fullName": "Jane Smith"
        }
      },
      "salary": 85000,
      "workLocation": "Building A, Floor 3"
    }
  ],
  "total": 25,
  "direct": 5,
  "fromSubDepartments": 20,
  "department": {
    "name": "Engineering",
    "code": "ENG"
  },
  "page": 1,
  "limit": 10
}
```

### Add Employee to Department

Assign an employee to a department (Admin/HR only).

**Endpoint:** `POST /departments/{id}/employees`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "employeeId": "employee-uuid",
  "effectiveDate": "2025-09-15",
  "reason": "Team reorganization"
}
```

**Response:**
```json
{
  "data": {
    "employeeId": "employee-uuid",
    "departmentId": "dept-uuid",
    "previousDepartmentId": "old-dept-uuid",
    "effectiveDate": "2025-09-15",
    "transferredAt": "2025-09-03T14:00:00Z"
  },
  "message": "Employee added to department successfully"
}
```

### Remove Employee from Department

Remove an employee from a department (Admin/HR only).

**Endpoint:** `DELETE /departments/{id}/employees/{employeeId}`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "targetDepartmentId": "new-dept-uuid",
  "effectiveDate": "2025-09-20",
  "reason": "Transfer to new team"
}
```

**Response:**
```json
{
  "data": {
    "employeeId": "employee-uuid",
    "fromDepartmentId": "dept-uuid",
    "toDepartmentId": "new-dept-uuid",
    "effectiveDate": "2025-09-20",
    "transferredAt": "2025-09-03T14:30:00Z"
  },
  "message": "Employee transferred successfully"
}
```

## Department Statistics

### Get Department Statistics

Get comprehensive department analytics.

**Endpoint:** `GET /departments/{id}/statistics`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `period` (string): Time period (month, quarter, year)
- `includeSubDepartments` (boolean): Include sub-department data
- `compareWithPrevious` (boolean): Include period-over-period comparison

**Response:**
```json
{
  "data": {
    "overview": {
      "totalEmployees": 25,
      "activeEmployees": 24,
      "directEmployees": 5,
      "subDepartmentEmployees": 20,
      "avgTenure": 2.3,
      "turnoverRate": 8.5
    },
    "performance": {
      "attendanceRate": 94.2,
      "averageWorkingHours": 8.4,
      "productivityScore": 87.5,
      "goalCompletionRate": 92.0
    },
    "compensation": {
      "totalPayroll": 212500,
      "avgSalary": 85000,
      "medianSalary": 82000,
      "budgetUtilization": 87.5,
      "overtimeCost": 15000
    },
    "demographics": {
      "avgAge": 31.8,
      "genderDistribution": {
        "male": 16,
        "female": 8,
        "other": 1
      },
      "experienceLevels": {
        "junior": 8,
        "mid": 12,
        "senior": 5
      }
    },
    "growth": {
      "newHires": 3,
      "departures": 1,
      "promotions": 2,
      "transfers": {
        "in": 1,
        "out": 2
      }
    },
    "subDepartments": [
      {
        "name": "Frontend Development",
        "employeeCount": 8,
        "attendanceRate": 96.1,
        "avgSalary": 78000
      }
    ]
  }
}
```

### Get All Departments Statistics

Get statistics for all departments (Admin/HR only).

**Endpoint:** `GET /departments/statistics`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `includeInactive` (boolean): Include inactive departments
- `sortBy` (string): Sort field (name, employeeCount, budget)
- `sortOrder` (string): Sort order (ASC, DESC)

**Response:**
```json
{
  "data": {
    "summary": {
      "totalDepartments": 8,
      "activeDepartments": 7,
      "totalEmployees": 45,
      "totalBudget": 1200000,
      "avgEmployeesPerDept": 5.6
    },
    "departments": [
      {
        "id": "dept-uuid-1",
        "name": "Engineering",
        "code": "ENG",
        "employeeCount": 25,
        "budget": 500000,
        "budgetUtilization": 87.5,
        "attendanceRate": 94.2,
        "turnoverRate": 8.5,
        "avgSalary": 85000
      }
    ],
    "trends": {
      "growthRate": 15.2,
      "budgetGrowth": 12.8,
      "productivityTrend": "increasing"
    },
    "topPerforming": [
      {
        "department": "Engineering",
        "metric": "productivity",
        "score": 94.2
      }
    ]
  }
}
```

## Department Budget Management

### Update Department Budget

Update department budget allocation (Admin/Finance only).

**Endpoint:** `PATCH /departments/{id}/budget`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "budget": 550000,
  "budgetCurrency": "USD",
  "budgetPeriod": "annual",
  "effectiveDate": "2025-10-01",
  "reason": "Budget increase for Q4 expansion"
}
```

**Response:**
```json
{
  "data": {
    "departmentId": "dept-uuid",
    "previousBudget": 500000,
    "newBudget": 550000,
    "budgetCurrency": "USD",
    "effectiveDate": "2025-10-01",
    "budgetIncrease": 50000,
    "increasePercentage": 10.0,
    "updatedAt": "2025-09-03T15:00:00Z"
  },
  "message": "Department budget updated successfully"
}
```

### Get Budget Utilization

Get budget utilization and spending analysis.

**Endpoint:** `GET /departments/{id}/budget/utilization`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `period` (string): Time period (month, quarter, year)
- `includeBreakdown` (boolean): Include spending breakdown

**Response:**
```json
{
  "data": {
    "budget": {
      "allocated": 500000,
      "used": 437500,
      "remaining": 62500,
      "utilizationRate": 87.5,
      "period": "2025"
    },
    "breakdown": {
      "salaries": 375000,
      "benefits": 37500,
      "equipment": 15000,
      "training": 8000,
      "other": 2000
    },
    "monthlyTrend": [
      {
        "month": "2025-01",
        "spent": 41250,
        "budgetUsed": 8.25
      }
    ],
    "projectedSpend": 525000,
    "budgetStatus": "on_track",
    "recommendations": [
      "Consider increasing training budget for Q4",
      "Equipment spending is below target"
    ]
  }
}
```

## Error Responses

### Common Error Codes

#### 400 Bad Request
```json
{
  "error": {
    "code": "CIRCULAR_HIERARCHY",
    "message": "Cannot set parent department - would create circular hierarchy",
    "statusCode": 400
  }
}
```

#### 422 Validation Error
```json
{
  "error": {
    "code": "DUPLICATE_DEPARTMENT_CODE",
    "message": "Department code 'ENG' already exists",
    "details": {
      "field": "code",
      "value": "ENG"
    },
    "statusCode": 422
  }
}
```

#### 409 Conflict
```json
{
  "error": {
    "code": "DEPARTMENT_HAS_EMPLOYEES",
    "message": "Cannot delete department with active employees",
    "details": {
      "employeeCount": 15,
      "suggestions": ["Transfer employees first", "Use force delete"]
    },
    "statusCode": 409
  }
}
```

## Integration Examples

### JavaScript/React

```javascript
class DepartmentService {
  constructor(baseURL = 'https://api.upsurgemedia.in') {
    this.api = axios.create({ baseURL });
  }

  setToken(token) {
    this.api.defaults.headers.common['Authorization'] = `Bearer ${token}`;
  }

  async getDepartments(params = {}) {
    const response = await this.api.get('/departments', { params });
    return response.data;
  }

  async getDepartment(id, params = {}) {
    const response = await this.api.get(`/departments/${id}`, { params });
    return response.data.data;
  }

  async getDepartmentHierarchy(params = {}) {
    const response = await this.api.get('/departments/hierarchy', { params });
    return response.data.data;
  }

  async createDepartment(departmentData) {
    const response = await this.api.post('/departments', departmentData);
    return response.data.data;
  }

  async updateDepartment(id, updates) {
    const response = await this.api.patch(`/departments/${id}`, updates);
    return response.data.data;
  }

  async getDepartmentEmployees(id, params = {}) {
    const response = await this.api.get(`/departments/${id}/employees`, { params });
    return response.data;
  }

  async transferEmployee(departmentId, employeeId, transferData) {
    const response = await this.api.post(`/departments/${departmentId}/employees`, {
      employeeId,
      ...transferData
    });
    return response.data.data;
  }

  async getDepartmentStatistics(id, params = {}) {
    const response = await this.api.get(`/departments/${id}/statistics`, { params });
    return response.data.data;
  }

  async updateDepartmentBudget(id, budgetData) {
    const response = await this.api.patch(`/departments/${id}/budget`, budgetData);
    return response.data.data;
  }

  async moveDepartment(id, moveData) {
    const response = await this.api.patch(`/departments/${id}/move`, moveData);
    return response.data.data;
  }
}

// Usage
const departmentService = new DepartmentService();
departmentService.setToken('your-jwt-token');

// Get organizational hierarchy
const hierarchy = await departmentService.getDepartmentHierarchy({
  format: 'tree',
  includeEmployeeCounts: true
});

// Create new department
const newDepartment = await departmentService.createDepartment({
  name: 'DevOps',
  code: 'DEVOPS',
  description: 'Infrastructure and deployment automation',
  parentDepartmentId: 'engineering-uuid',
  managerId: 'devops-manager-uuid'
});

// Get department statistics
const stats = await departmentService.getDepartmentStatistics('eng-uuid', {
  period: 'quarter',
  includeSubDepartments: true
});

console.log(`Department has ${stats.overview.totalEmployees} employees`);
console.log(`Attendance rate: ${stats.performance.attendanceRate}%`);
```

### Python

```python
import requests
from typing import Dict, List, Optional

class DepartmentAPI:
    def __init__(self, base_url: str = "https://api.upsurgemedia.in"):
        self.base_url = base_url
        self.session = requests.Session()

    def set_token(self, token: str):
        self.session.headers.update({"Authorization": f"Bearer {token}"})

    def get_departments(self, **params) -> Dict:
        response = self.session.get(f"{self.base_url}/departments", params=params)
        response.raise_for_status()
        return response.json()

    def get_department(self, department_id: str, **params) -> Dict:
        response = self.session.get(
            f"{self.base_url}/departments/{department_id}",
            params=params
        )
        response.raise_for_status()
        return response.json()["data"]

    def get_hierarchy(self, **params) -> Dict:
        response = self.session.get(
            f"{self.base_url}/departments/hierarchy",
            params=params
        )
        response.raise_for_status()
        return response.json()["data"]

    def create_department(self, department_data: Dict) -> Dict:
        response = self.session.post(
            f"{self.base_url}/departments",
            json=department_data
        )
        response.raise_for_status()
        return response.json()["data"]

    def update_department(self, department_id: str, updates: Dict) -> Dict:
        response = self.session.patch(
            f"{self.base_url}/departments/{department_id}",
            json=updates
        )
        response.raise_for_status()
        return response.json()["data"]

    def get_department_employees(self, department_id: str, **params) -> Dict:
        response = self.session.get(
            f"{self.base_url}/departments/{department_id}/employees",
            params=params
        )
        response.raise_for_status()
        return response.json()

    def get_department_statistics(self, department_id: str, **params) -> Dict:
        response = self.session.get(
            f"{self.base_url}/departments/{department_id}/statistics",
            params=params
        )
        response.raise_for_status()
        return response.json()["data"]

    def transfer_employee(self, from_dept_id: str, to_dept_id: str, 
                         employee_id: str, **transfer_data) -> Dict:
        response = self.session.post(
            f"{self.base_url}/departments/{to_dept_id}/employees",
            json={"employeeId": employee_id, **transfer_data}
        )
        response.raise_for_status()
        return response.json()["data"]

# Usage
api = DepartmentAPI()
api.set_token("your-jwt-token")

# Get organizational structure
hierarchy = api.get_hierarchy(
    format="tree",
    includeEmployeeCounts=True,
    maxLevel=3
)

print("Organizational Structure:")
for dept in hierarchy["hierarchy"]:
    print(f"- {dept['name']} ({dept['employeeCount']} employees)")
    for child in dept.get("children", []):
        print(f"  - {child['name']} ({child['employeeCount']} employees)")

# Create department
new_dept = api.create_department({
    "name": "Customer Success",
    "code": "CS",
    "description": "Customer support and success management",
    "managerId": "cs-manager-uuid",
    "budget": 250000
})
print(f"Created department: {new_dept['name']} (ID: {new_dept['id']})")

# Get department statistics
stats = api.get_department_statistics("engineering-uuid", period="quarter")
print(f"Engineering Department:")
print(f"- Total employees: {stats['overview']['totalEmployees']}")
print(f"- Attendance rate: {stats['performance']['attendanceRate']}%")
print(f"- Average salary: ${stats['compensation']['avgSalary']:,}")
```

---

The Departments API provides comprehensive organizational structure management with hierarchy support, employee assignment, budget tracking, and detailed analytics for effective organizational administration.
