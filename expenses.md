# Expenses API

The Expenses API manages expense reporting, approvals, reimbursements, and expense-related operations within the HRMS system.

## Endpoints Overview

| Method | Endpoint | Description | Access |
|--------|----------|-------------|---------|
| GET | `/expenses` | List expenses with filtering | All authenticated |
| GET | `/expenses/:id` | Get expense details | Owner, Approver, HR, Admin |
| POST | `/expenses` | Submit new expense | All authenticated |
| PUT | `/expenses/:id` | Update expense | Owner (if pending), HR, Admin |
| DELETE | `/expenses/:id` | Delete expense | Owner (if pending), Admin |
| POST | `/expenses/:id/approve` | Approve expense | Manager, HR, Admin |
| POST | `/expenses/:id/reject` | Reject expense | Manager, HR, Admin |
| POST | `/expenses/bulk-approve` | Bulk approve expenses | Manager, HR, Admin |
| GET | `/expenses/stats/overview` | Get expense statistics | HR, Admin |

## GET /expenses

Retrieve expenses with filtering and pagination.

### Request

```http
GET /api/expenses?page=1&limit=10&status=pending&category=travel&month=2024-02
Authorization: Bearer <token>
```

### Query Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| page | number | 1 | Page number for pagination |
| limit | number | 10 | Number of records per page |
| status | string | - | Filter by status (pending, approved, rejected, reimbursed) |
| category | string | - | Filter by expense category |
| employeeId | string | - | Filter by employee (Manager/HR/Admin only) |
| month | string | - | Filter by month (YYYY-MM format) |
| minAmount | number | - | Minimum expense amount |
| maxAmount | number | - | Maximum expense amount |
| dateFrom | string | - | Start date filter (YYYY-MM-DD) |
| dateTo | string | - | End date filter (YYYY-MM-DD) |

### Response

```json
{
  "success": true,
  "data": {
    "expenses": [
      {
        "id": "exp-001",
        "employeeId": "emp-001",
        "employee": {
          "id": "emp-001",
          "firstName": "John",
          "lastName": "Doe",
          "email": "john.doe@company.com",
          "department": "Sales"
        },
        "category": "travel",
        "subcategory": "hotel",
        "title": "Business trip hotel accommodation",
        "description": "Hotel stay for client meeting in New York",
        "amount": 450.00,
        "currency": "USD",
        "expenseDate": "2024-02-15",
        "submittedAt": "2024-02-16T09:00:00Z",
        "status": "pending",
        "approver": {
          "id": "mgr-001",
          "firstName": "Sarah",
          "lastName": "Johnson",
          "role": "manager"
        },
        "receipt": {
          "filename": "hotel_receipt_001.pdf",
          "url": "/api/documents/doc-receipt-001/download"
        },
        "reimbursementMethod": "bank_transfer",
        "businessPurpose": "Client meeting and contract negotiation",
        "tags": ["travel", "hotel", "client-meeting"],
        "mileage": null,
        "isRecurring": false
      }
    ],
    "pagination": {
      "page": 1,
      "limit": 10,
      "total": 45,
      "pages": 5
    },
    "summary": {
      "totalAmount": 12450.00,
      "pendingAmount": 3200.00,
      "approvedAmount": 8500.00,
      "rejectedAmount": 750.00
    }
  }
}
```

## GET /expenses/:id

Get detailed information about a specific expense.

### Request

```http
GET /api/expenses/exp-001
Authorization: Bearer <token>
```

### Response

```json
{
  "success": true,
  "data": {
    "expense": {
      "id": "exp-001",
      "employeeId": "emp-001",
      "employee": {
        "id": "emp-001",
        "firstName": "John",
        "lastName": "Doe",
        "email": "john.doe@company.com",
        "department": "Sales",
        "manager": {
          "id": "mgr-001",
          "firstName": "Sarah",
          "lastName": "Johnson"
        }
      },
      "category": "travel",
      "subcategory": "hotel",
      "title": "Business trip hotel accommodation",
      "description": "Hotel stay for client meeting in New York",
      "amount": 450.00,
      "currency": "USD",
      "exchangeRate": 1.0,
      "expenseDate": "2024-02-15",
      "submittedAt": "2024-02-16T09:00:00Z",
      "updatedAt": "2024-02-16T09:00:00Z",
      "status": "pending",
      "statusHistory": [
        {
          "status": "submitted",
          "changedBy": "emp-001",
          "changedAt": "2024-02-16T09:00:00Z",
          "comment": "Initial submission"
        }
      ],
      "approver": {
        "id": "mgr-001",
        "firstName": "Sarah",
        "lastName": "Johnson",
        "role": "manager",
        "email": "sarah.johnson@company.com"
      },
      "receipts": [
        {
          "id": "receipt-001",
          "filename": "hotel_receipt_001.pdf",
          "uploadedAt": "2024-02-16T08:55:00Z",
          "size": 256000,
          "url": "/api/documents/doc-receipt-001/download"
        }
      ],
      "reimbursementMethod": "bank_transfer",
      "bankDetails": {
        "accountName": "John Doe",
        "accountNumber": "**** 1234",
        "bankName": "First National Bank"
      },
      "businessPurpose": "Client meeting and contract negotiation",
      "project": "Project Alpha",
      "costCenter": "CC-001",
      "tags": ["travel", "hotel", "client-meeting"],
      "location": "New York, NY",
      "attendees": ["John Doe", "Client Representative"],
      "mileage": null,
      "vehicle": null,
      "isRecurring": false,
      "recurringPattern": null,
      "vatAmount": 45.00,
      "vatRate": 10,
      "netAmount": 405.00,
      "approvalWorkflow": [
        {
          "level": 1,
          "approver": "mgr-001",
          "approverName": "Sarah Johnson",
          "status": "pending",
          "required": true
        },
        {
          "level": 2,
          "approver": "hr-001",
          "approverName": "Jane Smith",
          "status": "pending",
          "required": false,
          "condition": "amount > 500"
        }
      ],
      "companyPolicy": {
        "dailyLimit": 200.00,
        "requiresReceipt": true,
        "approvalRequired": true,
        "notes": "Hotel expenses require prior approval for amounts over $300"
      }
    }
  }
}
```

## POST /expenses

Submit a new expense report.

### Request

```http
POST /api/expenses
Authorization: Bearer <token>
Content-Type: multipart/form-data

{
  "category": "travel",
  "subcategory": "hotel",
  "title": "Business trip hotel accommodation",
  "description": "Hotel stay for client meeting in New York",
  "amount": 450.00,
  "expenseDate": "2024-02-15",
  "businessPurpose": "Client meeting and contract negotiation",
  "reimbursementMethod": "bank_transfer",
  "receipt": [binary file data]
}
```

### Request Body Schema (multipart/form-data)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| category | string | Yes | Expense category |
| subcategory | string | No | Expense subcategory |
| title | string | Yes | Expense title |
| description | string | No | Detailed description |
| amount | number | Yes | Expense amount |
| currency | string | No | Currency code (default: USD) |
| expenseDate | string | Yes | Date of expense (YYYY-MM-DD) |
| businessPurpose | string | Yes | Business purpose/reason |
| reimbursementMethod | string | Yes | bank_transfer, cash, check |
| receipt | file | No | Receipt file (if category requires) |
| project | string | No | Project code |
| costCenter | string | No | Cost center code |
| tags | string | No | Comma-separated tags |
| location | string | No | Expense location |
| attendees | string | No | Comma-separated attendees |
| mileage | number | No | Miles traveled (for mileage expenses) |
| vehicle | string | No | Vehicle used |
| vatAmount | number | No | VAT/Tax amount |
| vatRate | number | No | VAT/Tax rate percentage |

### Response

```json
{
  "success": true,
  "message": "Expense submitted successfully",
  "data": {
    "expense": {
      "id": "exp-002",
      "category": "travel",
      "subcategory": "hotel",
      "title": "Business trip hotel accommodation",
      "amount": 450.00,
      "currency": "USD",
      "expenseDate": "2024-02-15",
      "submittedAt": "2024-02-16T15:30:00Z",
      "status": "pending",
      "approver": {
        "id": "mgr-001",
        "firstName": "Sarah",
        "lastName": "Johnson"
      }
    }
  }
}
```

## PUT /expenses/:id

Update an existing expense (only if status is pending).

### Request

```http
PUT /api/expenses/exp-001
Authorization: Bearer <token>
Content-Type: application/json

{
  "description": "Updated: Hotel stay for client meeting in New York",
  "amount": 475.00,
  "businessPurpose": "Client meeting, contract negotiation, and team dinner",
  "tags": ["travel", "hotel", "client-meeting", "team-dinner"]
}
```

### Request Body Schema

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| title | string | No | Updated expense title |
| description | string | No | Updated description |
| amount | number | No | Updated amount |
| businessPurpose | string | No | Updated business purpose |
| tags | array | No | Updated tags |
| location | string | No | Updated location |
| attendees | array | No | Updated attendees list |

### Response

```json
{
  "success": true,
  "message": "Expense updated successfully",
  "data": {
    "expense": {
      "id": "exp-001",
      "description": "Updated: Hotel stay for client meeting in New York",
      "amount": 475.00,
      "businessPurpose": "Client meeting, contract negotiation, and team dinner",
      "updatedAt": "2024-02-17T10:15:00Z"
    }
  }
}
```

## POST /expenses/:id/approve

Approve an expense (Manager/HR/Admin only).

### Request

```http
POST /api/expenses/exp-001/approve
Authorization: Bearer <token>
Content-Type: application/json

{
  "comment": "Approved. Hotel expense is within policy limits.",
  "approvedAmount": 450.00,
  "reimbursementDate": "2024-02-25"
}
```

### Request Body Schema

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| comment | string | No | Approval comment |
| approvedAmount | number | No | Approved amount (if different from requested) |
| reimbursementDate | string | No | Expected reimbursement date |

### Response

```json
{
  "success": true,
  "message": "Expense approved successfully",
  "data": {
    "expense": {
      "id": "exp-001",
      "status": "approved",
      "approvedBy": "mgr-001",
      "approvedAt": "2024-02-17T14:00:00Z",
      "approvedAmount": 450.00,
      "comment": "Approved. Hotel expense is within policy limits.",
      "reimbursementDate": "2024-02-25"
    }
  }
}
```

## POST /expenses/:id/reject

Reject an expense (Manager/HR/Admin only).

### Request

```http
POST /api/expenses/exp-001/reject
Authorization: Bearer <token>
Content-Type: application/json

{
  "reason": "Missing required receipt",
  "comment": "Please provide the original hotel receipt for processing."
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
  "message": "Expense rejected",
  "data": {
    "expense": {
      "id": "exp-001",
      "status": "rejected",
      "rejectedBy": "mgr-001",
      "rejectedAt": "2024-02-17T14:00:00Z",
      "rejectionReason": "Missing required receipt",
      "comment": "Please provide the original hotel receipt for processing."
    }
  }
}
```

## POST /expenses/bulk-approve

Bulk approve multiple expenses.

### Request

```http
POST /api/expenses/bulk-approve
Authorization: Bearer <token>
Content-Type: application/json

{
  "expenseIds": ["exp-001", "exp-002", "exp-003"],
  "comment": "Bulk approval for travel expenses",
  "reimbursementDate": "2024-02-28"
}
```

### Request Body Schema

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| expenseIds | array | Yes | Array of expense IDs to approve |
| comment | string | No | Common approval comment |
| reimbursementDate | string | No | Expected reimbursement date |

### Response

```json
{
  "success": true,
  "message": "3 expenses approved successfully",
  "data": {
    "approved": [
      {
        "id": "exp-001",
        "title": "Hotel accommodation",
        "amount": 450.00
      },
      {
        "id": "exp-002",
        "title": "Flight tickets",
        "amount": 650.00
      },
      {
        "id": "exp-003",
        "title": "Taxi fare",
        "amount": 75.00
      }
    ],
    "failed": [],
    "summary": {
      "totalApproved": 3,
      "totalAmount": 1175.00
    }
  }
}
```

## DELETE /expenses/:id

Delete an expense (only if status is pending).

### Request

```http
DELETE /api/expenses/exp-001
Authorization: Bearer <token>
```

### Response

```json
{
  "success": true,
  "message": "Expense deleted successfully"
}
```

## GET /expenses/stats/overview

Get expense statistics and analytics.

### Request

```http
GET /api/expenses/stats/overview?period=monthly&year=2024
Authorization: Bearer <token>
```

### Query Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| period | string | monthly | Period for stats (daily, weekly, monthly, yearly) |
| year | number | current | Year for statistics |
| month | number | current | Month for statistics (1-12) |
| department | string | - | Filter by department |

### Response

```json
{
  "success": true,
  "data": {
    "stats": {
      "overview": {
        "totalExpenses": 156,
        "totalAmount": 45750.00,
        "pendingExpenses": 12,
        "pendingAmount": 3200.00,
        "approvedExpenses": 132,
        "approvedAmount": 38950.00,
        "rejectedExpenses": 12,
        "rejectedAmount": 3600.00,
        "reimbursedAmount": 35200.00,
        "outstandingAmount": 3750.00
      },
      "byCategory": [
        {
          "category": "travel",
          "count": 45,
          "amount": 18500.00,
          "percentage": 40.4
        },
        {
          "category": "meals",
          "count": 38,
          "amount": 9200.00,
          "percentage": 20.1
        },
        {
          "category": "office",
          "count": 32,
          "amount": 7500.00,
          "percentage": 16.4
        },
        {
          "category": "equipment",
          "count": 25,
          "amount": 6800.00,
          "percentage": 14.9
        },
        {
          "category": "training",
          "count": 16,
          "amount": 3750.00,
          "percentage": 8.2
        }
      ],
      "byStatus": {
        "pending": {
          "count": 12,
          "amount": 3200.00,
          "avgProcessingTime": 2.5
        },
        "approved": {
          "count": 132,
          "amount": 38950.00,
          "avgProcessingTime": 1.8
        },
        "rejected": {
          "count": 12,
          "amount": 3600.00,
          "avgProcessingTime": 2.2
        }
      },
      "monthlyTrend": [
        {
          "month": "2024-01",
          "expenses": 42,
          "amount": 12500.00,
          "approved": 38,
          "rejected": 4
        },
        {
          "month": "2024-02",
          "expenses": 55,
          "amount": 16750.00,
          "approved": 51,
          "rejected": 4
        }
      ],
      "topExpenses": [
        {
          "id": "exp-045",
          "title": "Annual conference attendance",
          "employee": "John Doe",
          "amount": 1500.00,
          "category": "travel",
          "date": "2024-02-10"
        }
      ],
      "topSpenders": [
        {
          "employeeId": "emp-001",
          "employeeName": "John Doe",
          "department": "Sales",
          "totalAmount": 4500.00,
          "expenseCount": 15
        }
      ],
      "averageExpense": 293.27,
      "medianExpense": 185.00,
      "complianceRate": 92.3,
      "receiptRate": 87.8,
      "avgApprovalTime": 1.9,
      "policyViolations": 8
    }
  }
}
```

## Expense Categories

| Category | Description | Receipt Required | Daily Limit |
|----------|-------------|------------------|-------------|
| travel | Travel expenses | Yes | $500 |
| meals | Business meals | Yes | $100 |
| accommodation | Hotel/lodging | Yes | $300 |
| transportation | Taxi, ride-share, parking | Recommended | $150 |
| office | Office supplies | No | $200 |
| equipment | Equipment purchases | Yes | $1000 |
| training | Training and education | Yes | $2000 |
| entertainment | Client entertainment | Yes | $300 |
| communication | Phone, internet | No | $100 |
| other | Miscellaneous | Recommended | $100 |

## Expense Status Flow

```
submitted → pending → approved → reimbursed
                  ↘ rejected
```

| Status | Description | Actions Available |
|--------|-------------|-------------------|
| submitted | Just submitted by employee | Edit, Delete, Approve, Reject |
| pending | Under review | Approve, Reject |
| approved | Approved for reimbursement | Mark as Reimbursed |
| rejected | Rejected by approver | Resubmit (as new expense) |
| reimbursed | Payment processed | View only |

## Reimbursement Methods

| Method | Description | Processing Time |
|--------|-------------|-----------------|
| bank_transfer | Direct bank transfer | 2-3 business days |
| payroll | Add to next payroll | Next pay period |
| check | Physical check | 5-7 business days |
| petty_cash | Cash reimbursement | Same day |

## Error Responses

### Expense Not Found (404)

```json
{
  "success": false,
  "message": "Expense not found or access denied"
}
```

### Cannot Edit Approved Expense (400)

```json
{
  "success": false,
  "message": "Cannot edit expense. Status is approved."
}
```

### Receipt Required (400)

```json
{
  "success": false,
  "message": "Receipt is required for this expense category and amount."
}
```

### Amount Exceeds Limit (400)

```json
{
  "success": false,
  "message": "Expense amount exceeds daily limit of $500 for travel category."
}
```

## Usage Examples

### JavaScript Submit Expense

```javascript
// Submit expense with receipt
const submitExpense = async (expenseData, receiptFile) => {
  const formData = new FormData();
  
  // Add expense data
  Object.keys(expenseData).forEach(key => {
    formData.append(key, expenseData[key]);
  });
  
  // Add receipt file
  if (receiptFile) {
    formData.append('receipt', receiptFile);
  }
  
  const response = await fetch('/api/expenses', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${token}`
      // Don't set Content-Type for FormData
    },
    body: formData
  });
  
  return response.json();
};

// Approve expense
const approveExpense = async (expenseId, comment) => {
  const response = await fetch(`/api/expenses/${expenseId}/approve`, {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${token}`,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      comment,
      approvedAmount: null, // Use requested amount
      reimbursementDate: new Date(Date.now() + 7 * 24 * 60 * 60 * 1000).toISOString().split('T')[0] // 7 days from now
    })
  });
  
  return response.json();
};

// Get expense statistics
const getExpenseStats = async (period = 'monthly', year = new Date().getFullYear()) => {
  const response = await fetch(`/api/expenses/stats/overview?period=${period}&year=${year}`, {
    headers: {
      'Authorization': `Bearer ${token}`
    }
  });
  
  return response.json();
};
```

### React Expense Form

```javascript
import { useState } from 'react';

const ExpenseForm = ({ onSubmit }) => {
  const [expense, setExpense] = useState({
    category: 'travel',
    title: '',
    description: '',
    amount: '',
    expenseDate: new Date().toISOString().split('T')[0],
    businessPurpose: '',
    reimbursementMethod: 'bank_transfer'
  });
  const [receipt, setReceipt] = useState(null);
  const [loading, setLoading] = useState(false);

  const handleSubmit = async (e) => {
    e.preventDefault();
    setLoading(true);

    try {
      const formData = new FormData();
      
      // Add expense fields
      Object.keys(expense).forEach(key => {
        formData.append(key, expense[key]);
      });
      
      // Add receipt if provided
      if (receipt) {
        formData.append('receipt', receipt);
      }

      const response = await fetch('/api/expenses', {
        method: 'POST',
        headers: {
          'Authorization': `Bearer ${localStorage.getItem('token')}`
        },
        body: formData
      });

      const result = await response.json();
      
      if (result.success) {
        onSubmit(result.data.expense);
        // Reset form
        setExpense({
          category: 'travel',
          title: '',
          description: '',
          amount: '',
          expenseDate: new Date().toISOString().split('T')[0],
          businessPurpose: '',
          reimbursementMethod: 'bank_transfer'
        });
        setReceipt(null);
      } else {
        console.error('Submission failed:', result.message);
      }
    } catch (error) {
      console.error('Submission error:', error);
    } finally {
      setLoading(false);
    }
  };

  return (
    <form onSubmit={handleSubmit} className="expense-form">
      <select
        value={expense.category}
        onChange={(e) => setExpense({...expense, category: e.target.value})}
        required
      >
        <option value="travel">Travel</option>
        <option value="meals">Meals</option>
        <option value="accommodation">Accommodation</option>
        <option value="office">Office Supplies</option>
        <option value="other">Other</option>
      </select>

      <input
        type="text"
        placeholder="Expense Title"
        value={expense.title}
        onChange={(e) => setExpense({...expense, title: e.target.value})}
        required
      />

      <textarea
        placeholder="Description"
        value={expense.description}
        onChange={(e) => setExpense({...expense, description: e.target.value})}
      />

      <input
        type="number"
        step="0.01"
        placeholder="Amount"
        value={expense.amount}
        onChange={(e) => setExpense({...expense, amount: e.target.value})}
        required
      />

      <input
        type="date"
        value={expense.expenseDate}
        onChange={(e) => setExpense({...expense, expenseDate: e.target.value})}
        required
      />

      <textarea
        placeholder="Business Purpose"
        value={expense.businessPurpose}
        onChange={(e) => setExpense({...expense, businessPurpose: e.target.value})}
        required
      />

      <select
        value={expense.reimbursementMethod}
        onChange={(e) => setExpense({...expense, reimbursementMethod: e.target.value})}
        required
      >
        <option value="bank_transfer">Bank Transfer</option>
        <option value="payroll">Payroll</option>
        <option value="check">Check</option>
        <option value="petty_cash">Petty Cash</option>
      </select>

      <input
        type="file"
        accept=".pdf,.jpg,.jpeg,.png"
        onChange={(e) => setReceipt(e.target.files[0])}
      />

      <button type="submit" disabled={loading}>
        {loading ? 'Submitting...' : 'Submit Expense'}
      </button>
    </form>
  );
};
```
