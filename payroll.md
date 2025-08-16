# Payroll API

The Payroll API manages salary calculations, payroll processing, payslip generation, and payroll statistics.

## Endpoints Overview

| Method | Endpoint | Description | Access |
|--------|----------|-------------|---------|
| GET | `/payroll` | List payroll records | HR, Admin, Self |
| GET | `/payroll/:id` | Get payroll record by ID | HR, Admin, Self |
| POST | `/payroll` | Create payroll record | HR, Admin |
| PUT | `/payroll/:id` | Update payroll record | HR, Admin |
| DELETE | `/payroll/:id` | Delete payroll record | Admin |
| POST | `/payroll/process` | Process bulk payroll | HR, Admin |
| GET | `/payroll/payslip/:id` | Generate payslip | HR, Admin, Self |
| GET | `/payroll/stats/overview` | Get payroll statistics | HR, Admin |

## GET /payroll

Retrieve payroll records with filtering and pagination.

### Request

```http
GET /api/payroll?page=1&limit=10&month=2&year=2024&status=processed
Authorization: Bearer <token>
```

### Query Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| page | number | 1 | Page number for pagination |
| limit | number | 10 | Number of records per page |
| month | number | - | Filter by month (1-12) |
| year | number | - | Filter by year |
| status | string | - | Filter by status (draft, processed, paid) |
| userId | string | - | Filter by user ID (HR/Admin only) |

### Response

```json
{
  "success": true,
  "data": {
    "payroll": [
      {
        "id": "payroll-001",
        "userId": "user-001",
        "month": 2,
        "year": 2024,
        "basicSalary": 75000,
        "allowances": {
          "houseRent": 15000,
          "transport": 5000,
          "medical": 3000
        },
        "deductions": {
          "tax": 12000,
          "pf": 9000,
          "insurance": 2000
        },
        "grossSalary": 98000,
        "netSalary": 75000,
        "status": "processed",
        "payDate": "2024-02-28",
        "user": {
          "id": "user-001",
          "firstName": "John",
          "lastName": "Doe",
          "employeeId": "EMP001",
          "department": "Engineering"
        },
        "createdAt": "2024-02-25T10:00:00Z",
        "updatedAt": "2024-02-28T15:30:00Z"
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

## GET /payroll/:id

Get detailed payroll record information.

### Request

```http
GET /api/payroll/payroll-001
Authorization: Bearer <token>
```

### Response

```json
{
  "success": true,
  "data": {
    "payroll": {
      "id": "payroll-001",
      "userId": "user-001",
      "month": 2,
      "year": 2024,
      "basicSalary": 75000,
      "allowances": {
        "houseRent": 15000,
        "transport": 5000,
        "medical": 3000,
        "overtime": 2500
      },
      "deductions": {
        "tax": 12000,
        "pf": 9000,
        "insurance": 2000,
        "loan": 5000
      },
      "grossSalary": 100500,
      "totalDeductions": 28000,
      "netSalary": 72500,
      "workingDays": 22,
      "presentDays": 20,
      "leaveDays": 2,
      "overtimeHours": 10,
      "status": "processed",
      "payDate": "2024-02-28",
      "processedBy": "hr-001",
      "notes": "Regular payroll processing",
      "user": {
        "id": "user-001",
        "firstName": "John",
        "lastName": "Doe",
        "employeeId": "EMP001",
        "department": "Engineering",
        "designation": "Senior Developer",
        "bankAccount": {
          "accountNumber": "****1234",
          "bankName": "ABC Bank"
        }
      },
      "createdAt": "2024-02-25T10:00:00Z",
      "updatedAt": "2024-02-28T15:30:00Z"
    }
  }
}
```

## POST /payroll

Create a new payroll record.

### Request

```http
POST /api/payroll
Authorization: Bearer <token>
Content-Type: application/json

{
  "userId": "user-001",
  "month": 3,
  "year": 2024,
  "basicSalary": 75000,
  "allowances": {
    "houseRent": 15000,
    "transport": 5000,
    "medical": 3000
  },
  "deductions": {
    "tax": 12000,
    "pf": 9000,
    "insurance": 2000
  },
  "overtimeHours": 8,
  "notes": "March 2024 payroll"
}
```

### Request Body Schema

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| userId | string | Yes | Employee UUID |
| month | number | Yes | Payroll month (1-12) |
| year | number | Yes | Payroll year |
| basicSalary | number | Yes | Basic salary amount |
| allowances | object | No | Allowances breakdown |
| deductions | object | No | Deductions breakdown |
| overtimeHours | number | No | Overtime hours worked |
| notes | string | No | Additional notes |

### Response

```json
{
  "success": true,
  "message": "Payroll record created successfully",
  "data": {
    "payroll": {
      "id": "payroll-002",
      "userId": "user-001",
      "month": 3,
      "year": 2024,
      "basicSalary": 75000,
      "grossSalary": 98000,
      "netSalary": 75000,
      "status": "draft",
      "createdAt": "2024-03-01T10:00:00Z"
    }
  }
}
```

## PUT /payroll/:id

Update an existing payroll record.

### Request

```http
PUT /api/payroll/payroll-001
Authorization: Bearer <token>
Content-Type: application/json

{
  "allowances": {
    "houseRent": 16000,
    "transport": 5500,
    "medical": 3000,
    "bonus": 10000
  },
  "status": "processed",
  "notes": "Updated with performance bonus"
}
```

### Response

```json
{
  "success": true,
  "message": "Payroll record updated successfully",
  "data": {
    "payroll": {
      "id": "payroll-001",
      "grossSalary": 109500,
      "netSalary": 81500,
      "status": "processed",
      "updatedAt": "2024-02-28T16:00:00Z"
    }
  }
}
```

## POST /payroll/process

Process payroll for multiple employees in bulk.

### Request

```http
POST /api/payroll/process
Authorization: Bearer <token>
Content-Type: application/json

{
  "month": 3,
  "year": 2024,
  "userIds": ["user-001", "user-002", "user-003"],
  "processAll": false,
  "payDate": "2024-03-31"
}
```

### Request Body Schema

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| month | number | Yes | Processing month (1-12) |
| year | number | Yes | Processing year |
| userIds | array | No | Specific user IDs (if not processAll) |
| processAll | boolean | No | Process all active employees |
| payDate | date | No | Payment date |

### Response

```json
{
  "success": true,
  "message": "Bulk payroll processing initiated",
  "data": {
    "processedCount": 45,
    "failedCount": 2,
    "results": [
      {
        "userId": "user-001",
        "status": "success",
        "payrollId": "payroll-003"
      },
      {
        "userId": "user-002",
        "status": "failed",
        "error": "Missing bank account details"
      }
    ]
  }
}
```

## GET /payroll/payslip/:id

Generate and retrieve payslip for a payroll record.

### Request

```http
GET /api/payroll/payslip/payroll-001
Authorization: Bearer <token>
```

### Response

```json
{
  "success": true,
  "data": {
    "payslip": {
      "id": "payroll-001",
      "employeeDetails": {
        "name": "John Doe",
        "employeeId": "EMP001",
        "designation": "Senior Developer",
        "department": "Engineering",
        "joiningDate": "2024-01-15"
      },
      "payPeriod": {
        "month": "February",
        "year": 2024,
        "workingDays": 22,
        "presentDays": 20,
        "leaveDays": 2
      },
      "earnings": {
        "basicSalary": 75000,
        "houseRent": 15000,
        "transport": 5000,
        "medical": 3000,
        "overtime": 2500,
        "totalEarnings": 100500
      },
      "deductions": {
        "tax": 12000,
        "pf": 9000,
        "insurance": 2000,
        "loan": 5000,
        "totalDeductions": 28000
      },
      "netSalary": 72500,
      "payDate": "2024-02-28",
      "generatedAt": "2024-02-28T16:30:00Z"
    }
  }
}
```

## GET /payroll/stats/overview

Get payroll statistics and analytics.

### Request

```http
GET /api/payroll/stats/overview?year=2024
Authorization: Bearer <token>
```

### Query Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| year | number | Statistics year |
| month | number | Specific month statistics |

### Response

```json
{
  "success": true,
  "data": {
    "stats": {
      "totalEmployees": 45,
      "processedPayrolls": 43,
      "pendingPayrolls": 2,
      "totalPayrollAmount": 3375000,
      "averageSalary": 75000,
      "totalTaxDeducted": 540000,
      "totalPfDeducted": 405000,
      "byMonth": [
        {
          "month": 1,
          "processed": 45,
          "amount": 3375000,
          "avgSalary": 75000
        },
        {
          "month": 2,
          "processed": 43,
          "amount": 3225000,
          "avgSalary": 75000
        }
      ],
      "byDepartment": [
        {
          "department": "Engineering",
          "employees": 20,
          "totalAmount": 1500000,
          "avgSalary": 75000
        },
        {
          "department": "Sales",
          "employees": 15,
          "totalAmount": 900000,
          "avgSalary": 60000
        }
      ],
      "topEarners": [
        {
          "userId": "user-001",
          "name": "John Doe",
          "salary": 120000,
          "department": "Engineering"
        }
      ]
    }
  }
}
```

## Payroll Status Flow

```
draft → processed → paid
draft → cancelled
processed → cancelled (before payment)
```

## Salary Components

### Earnings
- **Basic Salary**: Base salary amount
- **House Rent Allowance (HRA)**: Housing allowance
- **Transport Allowance**: Travel allowance
- **Medical Allowance**: Health benefits
- **Overtime**: Additional hours compensation
- **Bonus**: Performance or festival bonus

### Deductions
- **Income Tax**: Tax deduction at source
- **Provident Fund (PF)**: Retirement savings
- **Insurance**: Medical/life insurance premiums
- **Loan EMI**: Employee loan repayment
- **Other**: Miscellaneous deductions

## Error Responses

### Payroll Already Exists (400)

```json
{
  "success": false,
  "message": "Payroll already exists for this employee and period"
}
```

### Invalid Period (400)

```json
{
  "success": false,
  "message": "Invalid month or year specified"
}
```

### Cannot Modify Paid Payroll (400)

```json
{
  "success": false,
  "message": "Cannot modify payroll that has already been paid"
}
```

## Usage Examples

### JavaScript

```javascript
// Create payroll record
const createPayroll = async (payrollData) => {
  const response = await fetch('/api/payroll', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${token}`,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify(payrollData)
  });
  return response.json();
};

// Process bulk payroll
const processBulkPayroll = async (month, year, userIds) => {
  const response = await fetch('/api/payroll/process', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${token}`,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      month,
      year,
      userIds,
      payDate: new Date().toISOString().split('T')[0]
    })
  });
  return response.json();
};

// Generate payslip
const generatePayslip = async (payrollId) => {
  const response = await fetch(`/api/payroll/payslip/${payrollId}`, {
    headers: {
      'Authorization': `Bearer ${token}`
    }
  });
  return response.json();
};

// Get payroll statistics
const getPayrollStats = async (year) => {
  const response = await fetch(`/api/payroll/stats/overview?year=${year}`, {
    headers: {
      'Authorization': `Bearer ${token}`
    }
  });
  return response.json();
};
```

### Payroll Processing Workflow

```javascript
// Complete payroll processing workflow
const processMonthlyPayroll = async (month, year) => {
  try {
    // Step 1: Get all active employees
    const employeesResponse = await fetch('/api/employees?status=active', {
      headers: { 'Authorization': `Bearer ${token}` }
    });
    const employees = await employeesResponse.json();

    // Step 2: Process payroll for all employees
    const processResponse = await fetch('/api/payroll/process', {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${token}`,
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({
        month,
        year,
        processAll: true,
        payDate: `${year}-${String(month).padStart(2, '0')}-28`
      })
    });

    const result = await processResponse.json();
    
    if (result.success) {
      console.log(`Processed ${result.data.processedCount} payrolls`);
      
      // Step 3: Handle failed payrolls
      if (result.data.failedCount > 0) {
        console.warn('Some payrolls failed:', result.data.results.filter(r => r.status === 'failed'));
      }
      
      return result;
    }
  } catch (error) {
    console.error('Payroll processing failed:', error);
    throw error;
  }
};
```
