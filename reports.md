# Reports API

The Reports API provides comprehensive reporting, analytics, and data visualization capabilities for the HRMS system across all modules.

## Endpoints Overview

| Method | Endpoint | Description | Access |
|--------|----------|-------------|---------|
| GET | `/reports` | List available reports | HR, Admin |
| GET | `/reports/:id` | Get specific report | HR, Admin |
| POST | `/reports/generate` | Generate custom report | HR, Admin |
| GET | `/reports/employee-summary` | Employee summary report | HR, Admin |
| GET | `/reports/attendance-summary` | Attendance analytics | Manager, HR, Admin |
| GET | `/reports/leave-analytics` | Leave trends and analytics | HR, Admin |
| GET | `/reports/expense-analysis` | Expense analysis report | Manager, HR, Admin |
| GET | `/reports/payroll-summary` | Payroll summary report | HR, Admin |
| GET | `/reports/timesheet-analytics` | Timesheet productivity reports | Manager, HR, Admin |
| GET | `/reports/department-metrics` | Department performance metrics | Manager, HR, Admin |
| POST | `/reports/export` | Export report data | HR, Admin |
| GET | `/reports/templates` | Get report templates | HR, Admin |

## GET /reports

List all available reports with filtering.

### Request

```http
GET /api/reports?category=hr&type=summary&access=admin
Authorization: Bearer <token>
```

### Query Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| category | string | - | Filter by category (hr, finance, operations) |
| type | string | - | Filter by type (summary, detailed, analytics) |
| access | string | - | Filter by access level |
| module | string | - | Filter by module (attendance, leave, payroll) |

### Response

```json
{
  "success": true,
  "data": {
    "reports": [
      {
        "id": "rpt-001",
        "name": "Employee Summary Report",
        "description": "Comprehensive overview of all employees including demographics, status, and key metrics",
        "category": "hr",
        "type": "summary",
        "module": "employees",
        "accessLevel": ["hr", "admin"],
        "parameters": [
          {
            "name": "department",
            "type": "select",
            "required": false,
            "options": ["Engineering", "Sales", "HR", "Finance"]
          },
          {
            "name": "status",
            "type": "select",
            "required": false,
            "options": ["active", "inactive", "all"]
          },
          {
            "name": "dateRange",
            "type": "dateRange",
            "required": false,
            "default": "last30Days"
          }
        ],
        "outputFormats": ["pdf", "excel", "csv"],
        "estimatedRunTime": "2-5 seconds",
        "lastUpdated": "2024-02-20T10:00:00Z",
        "isSchedulable": true,
        "tags": ["employee", "summary", "demographics"]
      },
      {
        "id": "rpt-002",
        "name": "Attendance Analytics",
        "description": "Detailed attendance patterns, trends, and compliance metrics",
        "category": "operations",
        "type": "analytics",
        "module": "attendance",
        "accessLevel": ["manager", "hr", "admin"],
        "parameters": [
          {
            "name": "period",
            "type": "select",
            "required": true,
            "options": ["weekly", "monthly", "quarterly", "yearly"],
            "default": "monthly"
          },
          {
            "name": "employeeId",
            "type": "employee_select",
            "required": false,
            "multiple": true
          },
          {
            "name": "department",
            "type": "select",
            "required": false,
            "options": ["Engineering", "Sales", "HR", "Finance"]
          }
        ],
        "outputFormats": ["pdf", "excel"],
        "estimatedRunTime": "10-30 seconds",
        "lastUpdated": "2024-02-19T15:30:00Z",
        "isSchedulable": true,
        "tags": ["attendance", "analytics", "compliance"]
      }
    ]
  }
}
```

## POST /reports/generate

Generate a custom report with specified parameters.

### Request

```http
POST /api/reports/generate
Authorization: Bearer <token>
Content-Type: application/json

{
  "reportId": "rpt-001",
  "parameters": {
    "department": "Engineering",
    "status": "active",
    "dateRange": {
      "startDate": "2024-01-01",
      "endDate": "2024-02-29"
    }
  },
  "outputFormat": "pdf",
  "includeCharts": true,
  "emailDelivery": {
    "enabled": true,
    "recipients": ["hr@company.com", "manager@company.com"]
  }
}
```

### Request Body Schema

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| reportId | string | Yes | Report template ID |
| parameters | object | No | Report parameters |
| outputFormat | string | No | Output format (pdf, excel, csv) |
| includeCharts | boolean | No | Include visual charts |
| emailDelivery | object | No | Email delivery options |
| scheduling | object | No | Schedule recurring reports |

### Response

```json
{
  "success": true,
  "message": "Report generated successfully",
  "data": {
    "report": {
      "id": "gen-rpt-001",
      "reportId": "rpt-001",
      "name": "Employee Summary Report - Engineering",
      "status": "completed",
      "generatedAt": "2024-02-21T14:30:00Z",
      "parameters": {
        "department": "Engineering",
        "status": "active",
        "dateRange": {
          "startDate": "2024-01-01",
          "endDate": "2024-02-29"
        }
      },
      "outputFormat": "pdf",
      "fileSize": 2048000,
      "downloadUrl": "/api/reports/gen-rpt-001/download",
      "expiresAt": "2024-03-21T14:30:00Z",
      "summary": {
        "totalEmployees": 45,
        "activeEmployees": 42,
        "inactiveEmployees": 3,
        "newHires": 5,
        "departures": 2
      }
    }
  }
}
```

## GET /reports/employee-summary

Generate employee summary report.

### Request

```http
GET /api/reports/employee-summary?department=Engineering&includeInactive=false&format=json
Authorization: Bearer <token>
```

### Query Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| department | string | - | Filter by department |
| includeInactive | boolean | false | Include inactive employees |
| format | string | json | Response format (json, pdf, excel) |
| groupBy | string | department | Group results by field |

### Response

```json
{
  "success": true,
  "data": {
    "report": {
      "title": "Employee Summary Report",
      "generatedAt": "2024-02-21T15:00:00Z",
      "period": {
        "startDate": "2024-01-01",
        "endDate": "2024-02-29"
      },
      "summary": {
        "totalEmployees": 145,
        "activeEmployees": 138,
        "inactiveEmployees": 7,
        "newHires": 12,
        "departures": 5,
        "averageTenure": 2.5,
        "averageAge": 32.4,
        "genderDistribution": {
          "male": 78,
          "female": 65,
          "other": 2
        }
      },
      "byDepartment": [
        {
          "department": "Engineering",
          "totalEmployees": 45,
          "activeEmployees": 42,
          "newHires": 5,
          "departures": 1,
          "averageTenure": 3.2,
          "averageSalary": 95000,
          "topSkills": ["JavaScript", "Python", "React", "Node.js"],
          "performance": {
            "excellent": 15,
            "good": 22,
            "satisfactory": 5,
            "needsImprovement": 0
          }
        },
        {
          "department": "Sales",
          "totalEmployees": 32,
          "activeEmployees": 30,
          "newHires": 3,
          "departures": 2,
          "averageTenure": 2.1,
          "averageSalary": 65000,
          "topSkills": ["CRM", "Lead Generation", "Negotiation"],
          "performance": {
            "excellent": 8,
            "good": 18,
            "satisfactory": 4,
            "needsImprovement": 0
          }
        }
      ],
      "demographics": {
        "ageGroups": {
          "20-25": 18,
          "26-30": 45,
          "31-35": 38,
          "36-40": 25,
          "41-45": 12,
          "46+": 7
        },
        "educationLevels": {
          "highSchool": 8,
          "bachelor": 78,
          "master": 52,
          "phd": 7
        },
        "tenureDistribution": {
          "0-1 years": 35,
          "1-2 years": 42,
          "2-5 years": 45,
          "5+ years": 23
        }
      },
      "trends": {
        "hiringTrend": [
          {"month": "2024-01", "hires": 6, "departures": 2},
          {"month": "2024-02", "hires": 6, "departures": 3}
        ],
        "departmentGrowth": [
          {"department": "Engineering", "growth": 12.5},
          {"department": "Sales", "growth": 8.3},
          {"department": "HR", "growth": -5.0}
        ]
      }
    }
  }
}
```

## GET /reports/attendance-summary

Generate attendance analytics report.

### Request

```http
GET /api/reports/attendance-summary?period=monthly&year=2024&month=2&department=Engineering
Authorization: Bearer <token>
```

### Response

```json
{
  "success": true,
  "data": {
    "report": {
      "title": "Attendance Analytics Report",
      "period": "February 2024",
      "generatedAt": "2024-02-29T23:59:00Z",
      "summary": {
        "totalEmployees": 145,
        "workingDays": 20,
        "totalWorkHours": 23200,
        "averageAttendance": 92.5,
        "punctualityRate": 87.3,
        "absenteeism": 7.5,
        "overtimeHours": 450
      },
      "departmentBreakdown": [
        {
          "department": "Engineering",
          "employees": 45,
          "attendanceRate": 94.2,
          "punctualityRate": 89.1,
          "averageHoursPerDay": 8.2,
          "overtimeHours": 180,
          "lateArrivals": 12,
          "earlyDepartures": 8
        },
        {
          "department": "Sales",
          "employees": 32,
          "attendanceRate": 91.8,
          "punctualityRate": 85.4,
          "averageHoursPerDay": 8.0,
          "overtimeHours": 95,
          "lateArrivals": 18,
          "earlyDepartures": 14
        }
      ],
      "attendancePatterns": {
        "dailyAverage": [
          {"day": "Monday", "attendance": 94.2, "avgHours": 8.3},
          {"day": "Tuesday", "attendance": 96.1, "avgHours": 8.4},
          {"day": "Wednesday", "attendance": 95.8, "avgHours": 8.2},
          {"day": "Thursday", "attendance": 94.9, "avgHours": 8.1},
          {"day": "Friday", "attendance": 88.7, "avgHours": 7.8}
        ],
        "weeklyTrends": [
          {"week": "2024-W05", "attendance": 93.2, "hours": 5800},
          {"week": "2024-W06", "attendance": 94.1, "hours": 5920},
          {"week": "2024-W07", "attendance": 91.8, "hours": 5640},
          {"week": "2024-W08", "attendance": 92.9, "hours": 5840}
        ]
      },
      "topPerformers": [
        {
          "employeeId": "emp-001",
          "name": "John Doe",
          "department": "Engineering",
          "attendanceRate": 100.0,
          "punctualityRate": 95.0,
          "totalHours": 168
        }
      ],
      "attendanceConcerns": [
        {
          "employeeId": "emp-045",
          "name": "Alice Johnson",
          "department": "Sales",
          "attendanceRate": 75.0,
          "absences": 5,
          "lateArrivals": 8,
          "reason": "Medical appointments"
        }
      ]
    }
  }
}
```

## GET /reports/leave-analytics

Generate leave trends and analytics report.

### Request

```http
GET /api/reports/leave-analytics?year=2024&includeTypes=vacation,sick,personal
Authorization: Bearer <token>
```

### Response

```json
{
  "success": true,
  "data": {
    "report": {
      "title": "Leave Analytics Report",
      "period": "2024",
      "generatedAt": "2024-02-29T16:00:00Z",
      "summary": {
        "totalLeaveRequests": 284,
        "approvedLeaves": 265,
        "rejectedLeaves": 8,
        "pendingLeaves": 11,
        "totalLeaveDays": 1420,
        "averageLeaveDays": 9.8,
        "approvalRate": 93.3,
        "leaveUtilization": 68.5
      },
      "leaveTypes": [
        {
          "type": "vacation",
          "requests": 156,
          "totalDays": 780,
          "averageDays": 5.0,
          "approvalRate": 96.2,
          "peakMonths": ["July", "December"]
        },
        {
          "type": "sick",
          "requests": 89,
          "totalDays": 267,
          "averageDays": 3.0,
          "approvalRate": 98.9,
          "peakMonths": ["January", "March"]
        },
        {
          "type": "personal",
          "requests": 39,
          "totalDays": 195,
          "averageDays": 5.0,
          "approvalRate": 84.6,
          "peakMonths": ["May", "September"]
        }
      ],
      "monthlyTrends": [
        {
          "month": "January",
          "requests": 28,
          "days": 140,
          "approvalRate": 92.9
        },
        {
          "month": "February",
          "requests": 22,
          "days": 110,
          "approvalRate": 95.5
        }
      ],
      "departmentAnalysis": [
        {
          "department": "Engineering",
          "employees": 45,
          "totalRequests": 89,
          "totalDays": 445,
          "averageDaysPerEmployee": 9.9,
          "mostCommonType": "vacation",
          "utilizationRate": 71.2
        },
        {
          "department": "Sales",
          "employees": 32,
          "totalRequests": 64,
          "totalDays": 320,
          "averageDaysPerEmployee": 10.0,
          "mostCommonType": "vacation",
          "utilizationRate": 66.7
        }
      ],
      "seasonalPatterns": {
        "Q1": {"requests": 68, "days": 340, "types": {"vacation": 30, "sick": 35, "personal": 3}},
        "Q2": {"requests": 85, "days": 425, "types": {"vacation": 58, "sick": 18, "personal": 9}},
        "Q3": {"requests": 78, "days": 390, "types": {"vacation": 48, "sick": 22, "personal": 8}},
        "Q4": {"requests": 53, "days": 265, "types": {"vacation": 20, "sick": 14, "personal": 19}}
      },
      "balanceAnalysis": {
        "lowBalance": 23,
        "mediumBalance": 89,
        "highBalance": 33,
        "averageBalance": 12.5,
        "utilizationDistribution": {
          "0-25%": 45,
          "26-50%": 52,
          "51-75%": 35,
          "76-100%": 13
        }
      }
    }
  }
}
```

## GET /reports/expense-analysis

Generate expense analysis and trends report.

### Request

```http
GET /api/reports/expense-analysis?period=quarterly&year=2024&quarter=1
Authorization: Bearer <token>
```

### Response

```json
{
  "success": true,
  "data": {
    "report": {
      "title": "Expense Analysis Report",
      "period": "Q1 2024",
      "generatedAt": "2024-03-31T23:59:00Z",
      "summary": {
        "totalExpenses": 156,
        "totalAmount": 45750.00,
        "averageExpense": 293.27,
        "approvedAmount": 42500.00,
        "rejectedAmount": 3250.00,
        "approvalRate": 92.9,
        "processingTime": 2.3,
        "complianceRate": 89.1
      },
      "categoryBreakdown": [
        {
          "category": "travel",
          "count": 45,
          "amount": 18500.00,
          "percentage": 40.4,
          "averageAmount": 411.11,
          "approvalRate": 95.6,
          "topExpenses": [
            {"description": "Flight to client meeting", "amount": 650.00},
            {"description": "Hotel accommodation", "amount": 450.00}
          ]
        },
        {
          "category": "meals",
          "count": 38,
          "amount": 9200.00,
          "percentage": 20.1,
          "averageAmount": 242.11,
          "approvalRate": 91.2
        },
        {
          "category": "office",
          "count": 32,
          "amount": 7500.00,
          "percentage": 16.4,
          "averageAmount": 234.38,
          "approvalRate": 87.5
        }
      ],
      "employeeAnalysis": [
        {
          "employeeId": "emp-001",
          "name": "John Doe",
          "department": "Sales",
          "totalExpenses": 12,
          "totalAmount": 3250.00,
          "averageExpense": 270.83,
          "topCategory": "travel",
          "complianceScore": 95.2
        }
      ],
      "monthlyTrends": [
        {"month": "January", "count": 52, "amount": 15200.00, "approval": 94.2},
        {"month": "February", "count": 48, "amount": 14300.00, "approval": 91.7},
        {"month": "March", "count": 56, "amount": 16250.00, "approval": 92.9}
      ],
      "policyCompliance": {
        "receiptCompliance": 87.8,
        "approvalCompliance": 92.9,
        "timingCompliance": 94.2,
        "amountCompliance": 89.1,
        "violations": [
          {
            "type": "missing_receipt",
            "count": 19,
            "impact": "medium"
          },
          {
            "type": "late_submission",
            "count": 9,
            "impact": "low"
          }
        ]
      },
      "costCenterAnalysis": [
        {
          "costCenter": "CC-001",
          "name": "Engineering",
          "budget": 25000.00,
          "spent": 18750.00,
          "utilization": 75.0,
          "remaining": 6250.00
        }
      ]
    }
  }
}
```

## GET /reports/payroll-summary

Generate payroll summary report.

### Request

```http
GET /api/reports/payroll-summary?period=2024-02&includeDeductions=true
Authorization: Bearer <token>
```

### Response

```json
{
  "success": true,
  "data": {
    "report": {
      "title": "Payroll Summary Report",
      "period": "February 2024",
      "generatedAt": "2024-02-29T18:00:00Z",
      "payrollDate": "2024-02-28",
      "summary": {
        "totalEmployees": 145,
        "totalGrossPay": 825000.00,
        "totalDeductions": 165000.00,
        "totalNetPay": 660000.00,
        "totalTaxes": 123750.00,
        "totalBenefits": 41250.00,
        "averageGrossPay": 5689.66,
        "averageNetPay": 4551.72
      },
      "departmentBreakdown": [
        {
          "department": "Engineering",
          "employees": 45,
          "totalGrossPay": 427500.00,
          "totalNetPay": 342000.00,
          "averageGrossPay": 9500.00,
          "averageNetPay": 7600.00,
          "overtimePay": 22500.00,
          "bonuses": 15000.00
        },
        {
          "department": "Sales",
          "employees": 32,
          "totalGrossPay": 208000.00,
          "totalNetPay": 166400.00,
          "averageGrossPay": 6500.00,
          "averageNetPay": 5200.00,
          "commissions": 31200.00,
          "bonuses": 8000.00
        }
      ],
      "compensationAnalysis": {
        "baseSalary": 742500.00,
        "overtimePay": 45000.00,
        "bonuses": 23000.00,
        "commissions": 31200.00,
        "allowances": 12500.00,
        "benefits": 41250.00
      },
      "deductionsBreakdown": {
        "federalTax": 82500.00,
        "stateTax": 41250.00,
        "socialSecurity": 51075.00,
        "medicare": 11962.50,
        "healthInsurance": 21750.00,
        "retirement401k": 33000.00,
        "other": 3462.50
      },
      "taxSummary": {
        "totalTaxable": 825000.00,
        "totalTaxes": 123750.00,
        "effectiveTaxRate": 15.0,
        "federalWithholding": 82500.00,
        "stateWithholding": 41250.00,
        "ficaTaxes": 63037.50
      },
      "benefitsSummary": [
        {
          "benefit": "Health Insurance",
          "enrolled": 132,
          "employeeContribution": 21750.00,
          "companyContribution": 43500.00
        },
        {
          "benefit": "401(k)",
          "enrolled": 118,
          "employeeContribution": 33000.00,
          "companyMatch": 16500.00
        }
      ],
      "yearToDateSummary": {
        "totalGrossPay": 1650000.00,
        "totalNetPay": 1320000.00,
        "totalTaxes": 247500.00,
        "totalBenefits": 82500.00
      }
    }
  }
}
```

## POST /reports/export

Export report data in various formats.

### Request

```http
POST /api/reports/export
Authorization: Bearer <token>
Content-Type: application/json

{
  "reportType": "employee-summary",
  "parameters": {
    "department": "Engineering",
    "dateRange": {
      "startDate": "2024-01-01",
      "endDate": "2024-02-29"
    }
  },
  "format": "excel",
  "includeCharts": true,
  "customFields": ["performance", "skills", "certifications"],
  "delivery": {
    "method": "email",
    "recipients": ["hr@company.com"]
  }
}
```

### Response

```json
{
  "success": true,
  "message": "Report export initiated successfully",
  "data": {
    "exportId": "exp-001",
    "status": "processing",
    "estimatedCompletion": "2024-02-21T15:10:00Z",
    "format": "excel",
    "fileSize": null,
    "downloadUrl": null,
    "deliveryMethod": "email"
  }
}
```

## Report Categories and Types

### Categories

| Category | Description | Access Level |
|----------|-------------|--------------|
| hr | Human Resources reports | HR, Admin |
| finance | Financial and payroll reports | Finance, HR, Admin |
| operations | Operational metrics | Manager, HR, Admin |
| compliance | Compliance and audit reports | HR, Admin |
| analytics | Advanced analytics and insights | Manager, HR, Admin |

### Report Types

| Type | Description | Complexity |
|------|-------------|------------|
| summary | High-level overview reports | Low |
| detailed | Comprehensive detailed reports | Medium |
| analytics | Trend analysis and insights | High |
| compliance | Regulatory compliance reports | Medium |
| custom | User-defined custom reports | Variable |

## Output Formats

| Format | Description | Features |
|--------|-------------|----------|
| PDF | Formatted document | Charts, branding, print-ready |
| Excel | Spreadsheet format | Formulas, pivot tables, charts |
| CSV | Comma-separated values | Data export, integrations |
| JSON | Structured data | API consumption, web apps |

## Error Responses

### Report Not Found (404)

```json
{
  "success": false,
  "message": "Report template not found"
}
```

### Insufficient Permissions (403)

```json
{
  "success": false,
  "message": "Insufficient permissions to access this report"
}
```

### Invalid Parameters (400)

```json
{
  "success": false,
  "message": "Invalid report parameters",
  "details": {
    "dateRange": "Start date must be before end date",
    "department": "Invalid department specified"
  }
}
```

### Report Generation Failed (500)

```json
{
  "success": false,
  "message": "Report generation failed due to system error"
}
```

## Usage Examples

### JavaScript Report Generation

```javascript
// Generate employee summary report
const generateEmployeeReport = async (department, format = 'pdf') => {
  const response = await fetch('/api/reports/generate', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${token}`,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      reportId: 'rpt-001',
      parameters: {
        department,
        status: 'active',
        dateRange: {
          startDate: '2024-01-01',
          endDate: '2024-02-29'
        }
      },
      outputFormat: format,
      includeCharts: true
    })
  });
  
  return response.json();
};

// Get attendance analytics
const getAttendanceAnalytics = async (period, year, month) => {
  const params = new URLSearchParams({
    period,
    year: year.toString(),
    month: month.toString()
  });
  
  const response = await fetch(`/api/reports/attendance-summary?${params}`, {
    headers: {
      'Authorization': `Bearer ${token}`
    }
  });
  
  return response.json();
};

// Export report with email delivery
const exportReport = async (reportType, parameters, format, recipients) => {
  const response = await fetch('/api/reports/export', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${token}`,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      reportType,
      parameters,
      format,
      includeCharts: true,
      delivery: {
        method: 'email',
        recipients
      }
    })
  });
  
  return response.json();
};
```

### React Report Dashboard

```javascript
import { useState, useEffect } from 'react';

const ReportDashboard = () => {
  const [reports, setReports] = useState([]);
  const [selectedReport, setSelectedReport] = useState(null);
  const [parameters, setParameters] = useState({});
  const [loading, setLoading] = useState(false);
  const [reportData, setReportData] = useState(null);

  useEffect(() => {
    loadAvailableReports();
  }, []);

  const loadAvailableReports = async () => {
    try {
      const response = await fetch('/api/reports', {
        headers: {
          'Authorization': `Bearer ${localStorage.getItem('token')}`
        }
      });
      
      const result = await response.json();
      
      if (result.success) {
        setReports(result.data.reports);
      }
    } catch (error) {
      console.error('Error loading reports:', error);
    }
  };

  const generateReport = async () => {
    if (!selectedReport) return;
    
    setLoading(true);
    try {
      const response = await fetch('/api/reports/generate', {
        method: 'POST',
        headers: {
          'Authorization': `Bearer ${localStorage.getItem('token')}`,
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({
          reportId: selectedReport.id,
          parameters,
          outputFormat: 'json',
          includeCharts: true
        })
      });

      const result = await response.json();
      
      if (result.success) {
        // If JSON format, display data directly
        if (result.data.report.data) {
          setReportData(result.data.report.data);
        } else {
          // For other formats, provide download link
          window.open(result.data.report.downloadUrl, '_blank');
        }
      }
    } catch (error) {
      console.error('Error generating report:', error);
    } finally {
      setLoading(false);
    }
  };

  const handleParameterChange = (paramName, value) => {
    setParameters({
      ...parameters,
      [paramName]: value
    });
  };

  return (
    <div className="report-dashboard">
      <div className="dashboard-header">
        <h1>Reports Dashboard</h1>
      </div>

      <div className="dashboard-content">
        <div className="report-selector">
          <h3>Select Report</h3>
          <div className="reports-grid">
            {reports.map(report => (
              <div
                key={report.id}
                className={`report-card ${selectedReport?.id === report.id ? 'selected' : ''}`}
                onClick={() => setSelectedReport(report)}
              >
                <h4>{report.name}</h4>
                <p>{report.description}</p>
                <div className="report-meta">
                  <span className="category">{report.category}</span>
                  <span className="type">{report.type}</span>
                </div>
              </div>
            ))}
          </div>
        </div>

        {selectedReport && (
          <div className="report-parameters">
            <h3>Report Parameters</h3>
            {selectedReport.parameters.map(param => (
              <div key={param.name} className="parameter-group">
                <label>{param.name}</label>
                {param.type === 'select' ? (
                  <select
                    value={parameters[param.name] || ''}
                    onChange={(e) => handleParameterChange(param.name, e.target.value)}
                  >
                    <option value="">All</option>
                    {param.options.map(option => (
                      <option key={option} value={option}>
                        {option}
                      </option>
                    ))}
                  </select>
                ) : param.type === 'dateRange' ? (
                  <div className="date-range">
                    <input
                      type="date"
                      placeholder="Start Date"
                      onChange={(e) => handleParameterChange(param.name, {
                        ...parameters[param.name],
                        startDate: e.target.value
                      })}
                    />
                    <input
                      type="date"
                      placeholder="End Date"
                      onChange={(e) => handleParameterChange(param.name, {
                        ...parameters[param.name],
                        endDate: e.target.value
                      })}
                    />
                  </div>
                ) : (
                  <input
                    type="text"
                    value={parameters[param.name] || ''}
                    onChange={(e) => handleParameterChange(param.name, e.target.value)}
                    placeholder={`Enter ${param.name}`}
                  />
                )}
              </div>
            ))}
            
            <div className="report-actions">
              <button 
                onClick={generateReport} 
                disabled={loading}
                className="generate-btn"
              >
                {loading ? 'Generating...' : 'Generate Report'}
              </button>
            </div>
          </div>
        )}

        {reportData && (
          <div className="report-results">
            <h3>Report Results</h3>
            <div className="report-content">
              {/* Render report data based on type */}
              <ReportViewer data={reportData} reportType={selectedReport?.type} />
            </div>
          </div>
        )}
      </div>
    </div>
  );
};

const ReportViewer = ({ data, reportType }) => {
  if (reportType === 'summary') {
    return <SummaryReportView data={data} />;
  } else if (reportType === 'analytics') {
    return <AnalyticsReportView data={data} />;
  } else {
    return <DetailedReportView data={data} />;
  }
};

const SummaryReportView = ({ data }) => (
  <div className="summary-report">
    <div className="summary-stats">
      {Object.entries(data.summary || {}).map(([key, value]) => (
        <div key={key} className="stat-card">
          <div className="stat-label">{key.replace(/([A-Z])/g, ' $1').toLowerCase()}</div>
          <div className="stat-value">{value}</div>
        </div>
      ))}
    </div>
    
    {data.byDepartment && (
      <div className="department-breakdown">
        <h4>Department Breakdown</h4>
        <div className="departments-grid">
          {data.byDepartment.map(dept => (
            <div key={dept.department} className="dept-card">
              <h5>{dept.department}</h5>
              <div className="dept-stats">
                <span>Employees: {dept.totalEmployees}</span>
                <span>Active: {dept.activeEmployees}</span>
              </div>
            </div>
          ))}
        </div>
      </div>
    )}
  </div>
);

const AnalyticsReportView = ({ data }) => (
  <div className="analytics-report">
    <div className="charts-container">
      {/* Chart components would go here */}
      <div className="chart-placeholder">
        Charts and analytics visualizations
      </div>
    </div>
    
    <div className="trends-section">
      {data.trends && (
        <div className="trends-data">
          <h4>Trends</h4>
          {/* Render trends data */}
        </div>
      )}
    </div>
  </div>
);

const DetailedReportView = ({ data }) => (
  <div className="detailed-report">
    <div className="data-table">
      {/* Render detailed data in table format */}
      <table>
        <thead>
          <tr>
            {Object.keys(data[0] || {}).map(key => (
              <th key={key}>{key}</th>
            ))}
          </tr>
        </thead>
        <tbody>
          {data.map((row, index) => (
            <tr key={index}>
              {Object.values(row).map((value, idx) => (
                <td key={idx}>{value}</td>
              ))}
            </tr>
          ))}
        </tbody>
      </table>
    </div>
  </div>
);
```

The Reports API provides comprehensive reporting and analytics capabilities for all aspects of the HRMS system. The API supports various output formats, scheduling, and automated delivery options to meet diverse reporting needs.
