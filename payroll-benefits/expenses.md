# 💰 Expenses API

The Expenses API manages employee expense reports, reimbursements, and approval workflows.

## Base URL
```
https://api.upsurgemedia.in/api/v1/expenses
```

## Endpoints Overview

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/expenses` | Create expense report |
| GET | `/expenses` | List all expenses |
| GET | `/expenses/stats` | Get expense statistics |
| GET | `/expenses/pending-approvals` | Get pending approvals |
| GET | `/expenses/report` | Generate expense report |
| GET | `/expenses/employee/:employeeId` | Get employee expenses |
| GET | `/expenses/my-expenses` | Get current user expenses |
| GET | `/expenses/:id` | Get expense details |
| PATCH | `/expenses/:id` | Update expense |
| POST | `/expenses/:id/submit` | Submit for approval |
| POST | `/expenses/:id/approve` | Approve expense |
| POST | `/expenses/:id/reimburse` | Mark as reimbursed |
| POST | `/expenses/:id/cancel` | Cancel expense |
| POST | `/expenses/bulk-action` | Bulk operations |
| DELETE | `/expenses/:id` | Delete expense |

## Expense Categories
- **Travel**: Transportation, lodging, meals
- **Office**: Supplies, equipment, software
- **Training**: Courses, conferences, certifications
- **Entertainment**: Client meetings, team events
- **Communication**: Phone, internet, subscriptions
- **Other**: Miscellaneous business expenses

## Expense Status
- **Draft**: Being prepared
- **Submitted**: Awaiting approval
- **Approved**: Approved for reimbursement
- **Rejected**: Not approved
- **Reimbursed**: Payment processed
- **Cancelled**: Cancelled by employee

## Authentication
All endpoints require JWT authentication:
```
Authorization: Bearer <your-jwt-token>
```

## Examples

### Create Expense Report
```bash
curl -X POST "https://api.upsurgemedia.in/api/v1/expenses" \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Client Meeting Lunch",
    "description": "Lunch meeting with ABC Corp client",
    "category": "entertainment",
    "type": "reimbursable",
    "amount": 85.50,
    "currency": "USD",
    "expenseDate": "2024-01-20",
    "vendor": "Restaurant XYZ",
    "businessPurpose": "Client relationship building"
  }'
```

### Submit for Approval
```bash
curl -X POST "https://api.upsurgemedia.in/api/v1/expenses/123/submit" \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{
    "notes": "Expense ready for review"
  }'
```

### Get Pending Approvals
```bash
curl -X GET "https://api.upsurgemedia.in/api/v1/expenses/pending-approvals" \
  -H "Authorization: Bearer <token>"
```
