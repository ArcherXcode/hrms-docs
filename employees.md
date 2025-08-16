# Employees API

The Employees API manages all employee-related operations including CRUD operations, statistics, and organizational structure.

## Endpoints Overview

| Method | Endpoint | Description | Access |
|--------|----------|-------------|---------|
| GET | `/employees` | List all employees | Admin, HR, Manager |
| GET | `/employees/:id` | Get employee by ID | Admin, HR, Manager, Self |
| POST | `/employees` | Create new employee | Admin, HR |
| PUT | `/employees/:id` | Update employee | Admin, HR, Self |
| DELETE | `/employees/:id` | Delete employee | Admin, HR |
| GET | `/employees/stats/overview` | Get employee statistics | Admin, HR |

## GET /employees

Retrieve a paginated list of employees with filtering and search capabilities.

### Request

```http
GET /api/employees?page=1&limit=10&search=john&department=engineering&role=employee
Authorization: Bearer <token>
```

### Query Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| page | number | 1 | Page number for pagination |
| limit | number | 10 | Number of records per page |
| search | string | - | Search in name, email, employee ID |
| department | string | - | Filter by department ID |
| status | string | - | Filter by status (active, terminated) |
| role | string | - | Filter by role (admin, hr, manager, employee) |

### Response

```json
{
  "success": true,
  "data": {
    "employees": [
      {
        "id": "550e8400-e29b-41d4-a716-446655440000",
        "employeeId": "EMP001",
        "firstName": "John",
        "lastName": "Doe",
        "email": "john.doe@company.com",
        "phone": "+1234567890",
        "designation": "Senior Developer",
        "role": "employee",
        "status": "active",
        "joiningDate": "2024-01-15",
        "salary": 75000,
        "department": {
          "id": "dept-001",
          "name": "Engineering"
        },
        "manager": {
          "id": "mgr-001",
          "firstName": "Jane",
          "lastName": "Smith"
        }
      }
    ],
    "pagination": {
      "page": 1,
      "limit": 10,
      "total": 45,
      "pages": 5
    }
  }
}
```

## GET /employees/:id

Get detailed information about a specific employee.

### Request

```http
GET /api/employees/550e8400-e29b-41d4-a716-446655440000
Authorization: Bearer <token>
```

### Response

```json
{
  "success": true,
  "data": {
    "employee": {
      "id": "550e8400-e29b-41d4-a716-446655440000",
      "organizationId": "org-001",
      "employeeId": "EMP001",
      "firstName": "John",
      "lastName": "Doe",
      "email": "john.doe@company.com",
      "phone": "+1234567890",
      "address": "123 Main St, City, State",
      "dateOfBirth": "1990-05-15",
      "designation": "Senior Developer",
      "role": "employee",
      "status": "active",
      "joiningDate": "2024-01-15",
      "salary": 75000,
      "emergencyContact": "+0987654321",
      "department": {
        "id": "dept-001",
        "name": "Engineering"
      },
      "manager": {
        "id": "mgr-001",
        "firstName": "Jane",
        "lastName": "Smith"
      },
      "permissions": ["read:profile", "write:profile"],
      "preferences": {
        "theme": "dark",
        "language": "en",
        "timezone": "UTC",
        "notifications": {
          "email": true,
          "push": true,
          "slack": false
        }
      },
      "createdAt": "2024-01-10T00:00:00Z",
      "updatedAt": "2024-01-15T00:00:00Z"
    }
  }
}
```

## POST /employees

Create a new employee record.

### Request

```http
POST /api/employees
Authorization: Bearer <token>
Content-Type: application/json

{
  "firstName": "Alice",
  "lastName": "Johnson",
  "email": "alice.johnson@company.com",
  "phone": "+1234567890",
  "departmentId": "dept-001",
  "designation": "Frontend Developer",
  "managerId": "mgr-001",
  "salary": 65000,
  "joiningDate": "2024-02-01",
  "address": "456 Oak St, City, State",
  "emergencyContact": "+0987654321",
  "dateOfBirth": "1992-08-20",
  "role": "employee"
}
```

### Request Body Schema

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| firstName | string | Yes | Employee's first name |
| lastName | string | Yes | Employee's last name |
| email | string | Yes | Valid email address |
| phone | string | No | Phone number |
| departmentId | string | No | Department UUID |
| designation | string | No | Job title |
| managerId | string | No | Manager's UUID |
| salary | number | No | Annual salary |
| joiningDate | date | No | Date of joining |
| address | string | No | Home address |
| emergencyContact | string | No | Emergency contact number |
| dateOfBirth | date | No | Date of birth |
| role | string | No | Role (admin, hr, manager, employee) |

### Response

```json
{
  "success": true,
  "message": "Employee created successfully",
  "data": {
    "employee": {
      "id": "550e8400-e29b-41d4-a716-446655440001",
      "employeeId": "EMP046",
      "firstName": "Alice",
      "lastName": "Johnson",
      "email": "alice.johnson@company.com",
      "status": "active",
      "createdAt": "2024-02-01T10:00:00Z"
    }
  }
}
```

## PUT /employees/:id

Update an existing employee's information.

### Request

```http
PUT /api/employees/550e8400-e29b-41d4-a716-446655440000
Authorization: Bearer <token>
Content-Type: application/json

{
  "designation": "Lead Developer",
  "salary": 85000,
  "phone": "+1234567899"
}
```

### Response

```json
{
  "success": true,
  "message": "Employee updated successfully",
  "data": {
    "employee": {
      "id": "550e8400-e29b-41d4-a716-446655440000",
      "designation": "Lead Developer",
      "salary": 85000,
      "phone": "+1234567899",
      "updatedAt": "2024-02-15T10:00:00Z"
    }
  }
}
```

## DELETE /employees/:id

Soft delete an employee (sets status to 'terminated').

### Request

```http
DELETE /api/employees/550e8400-e29b-41d4-a716-446655440000
Authorization: Bearer <token>
```

### Response

```json
{
  "success": true,
  "message": "Employee deleted successfully"
}
```

## GET /employees/stats/overview

Get organization-wide employee statistics.

### Request

```http
GET /api/employees/stats/overview
Authorization: Bearer <token>
```

### Response

```json
{
  "success": true,
  "data": {
    "stats": {
      "total": 45,
      "byStatus": {
        "active": 42,
        "terminated": 3
      },
      "byRole": {
        "employee": 35,
        "manager": 8,
        "hr": 1,
        "admin": 1
      }
    }
  }
}
```

## Error Responses

### Validation Error (400)

```json
{
  "success": false,
  "message": "Validation error",
  "error": "\"email\" must be a valid email"
}
```

### Not Found (404)

```json
{
  "success": false,
  "message": "Employee not found"
}
```

### Duplicate Email (400)

```json
{
  "success": false,
  "message": "Employee with this email already exists"
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

### JavaScript/TypeScript

```javascript
// Fetch employees list
const fetchEmployees = async (page = 1, search = '') => {
  const response = await fetch(`/api/employees?page=${page}&search=${search}`, {
    headers: {
      'Authorization': `Bearer ${token}`
    }
  });
  return response.json();
};

// Create new employee
const createEmployee = async (employeeData) => {
  const response = await fetch('/api/employees', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${token}`,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify(employeeData)
  });
  return response.json();
};

// Update employee
const updateEmployee = async (id, updates) => {
  const response = await fetch(`/api/employees/${id}`, {
    method: 'PUT',
    headers: {
      'Authorization': `Bearer ${token}`,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify(updates)
  });
  return response.json();
};
```

### cURL

```bash
# List employees
curl -X GET "https://api.company.com/api/employees?page=1&limit=10" \
  -H "Authorization: Bearer <token>"

# Create employee
curl -X POST "https://api.company.com/api/employees" \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{
    "firstName": "Alice",
    "lastName": "Johnson",
    "email": "alice.johnson@company.com"
  }'

# Update employee
curl -X PUT "https://api.company.com/api/employees/550e8400-e29b-41d4-a716-446655440000" \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{"designation": "Lead Developer"}'
```
