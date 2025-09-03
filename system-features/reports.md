# 📊 Reports API

The Reports API generates various analytical reports for HR, attendance, payroll, and business insights.

## Base URL
```
https://api.upsurgemedia.in/api/v1/reports
```

## Endpoints Overview

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/reports/generate` | Generate custom report |
| GET | `/reports` | List all reports |
| GET | `/reports/analytics/dashboard` | Get dashboard analytics |
| GET | `/reports/:id` | Get report details |
| GET | `/reports/:id/download` | Download report file |
| DELETE | `/reports/:id` | Delete report |

## Report Types

### Attendance Reports
- **Daily Attendance**: Daily attendance summary
- **Monthly Summary**: Monthly attendance patterns
- **Overtime Report**: Overtime hours and costs
- **Late Arrivals**: Late arrival tracking
- **Absence Report**: Employee absence analysis

### Payroll Reports
- **Payroll Summary**: Monthly payroll overview
- **Salary Breakdown**: Detailed salary components
- **Tax Report**: Tax deductions and withholdings
- **Bonus Report**: Bonus and incentive payments
- **Expense Report**: Employee expense analysis

### HR Reports
- **Employee Demographics**: Workforce analytics
- **Department Analysis**: Department-wise metrics
- **Turnover Report**: Employee retention analysis
- **Performance Review**: Performance evaluation data
- **Training Report**: Training completion tracking

### Leave Reports
- **Leave Balance**: Current leave balances
- **Leave Utilization**: Leave usage patterns
- **Leave Forecast**: Projected leave requirements
- **Approval Status**: Leave approval analytics

## Report Formats
- **PDF**: Formatted PDF documents
- **Excel**: Spreadsheet format (.xlsx)
- **CSV**: Comma-separated values
- **JSON**: Machine-readable format

## Authentication
All endpoints require JWT authentication:
```
Authorization: Bearer <your-jwt-token>
```

## Examples

### Generate Attendance Report
```bash
curl -X POST "https://api.upsurgemedia.in/api/v1/reports/generate" \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{
    "type": "attendance",
    "subType": "monthly_summary",
    "format": "pdf",
    "parameters": {
      "startDate": "2024-01-01",
      "endDate": "2024-01-31",
      "departments": ["engineering", "sales"],
      "includeOvertime": true
    }
  }'
```

### Get Dashboard Analytics
```bash
curl -X GET "https://api.upsurgemedia.in/api/v1/reports/analytics/dashboard" \
  -H "Authorization: Bearer <token>"
```

### Download Report
```bash
curl -X GET "https://api.upsurgemedia.in/api/v1/reports/report-123/download" \
  -H "Authorization: Bearer <token>" \
  -o "attendance_report.pdf"
```

## Response Format

### Report Generation Response
```json
{
  "success": true,
  "data": {
    "reportId": "report-123",
    "status": "generating",
    "type": "attendance",
    "format": "pdf",
    "estimatedCompletionTime": "2024-01-20T15:30:00Z"
  }
}
```

### Dashboard Analytics Response
```json
{
  "success": true,
  "data": {
    "attendance": {
      "todayPresent": 85,
      "todayAbsent": 5,
      "avgAttendanceRate": 94.2
    },
    "employees": {
      "total": 90,
      "active": 88,
      "onLeave": 2
    },
    "payroll": {
      "currentMonthTotal": 125000,
      "pendingApprovals": 3
    }
  }
}
```
