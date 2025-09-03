# 💰 Payroll API

The Payroll API provides comprehensive payroll management functionality including salary calculations, deductions, benefits, tax calculations, payslip generation, and compliance reporting.

## Base Endpoint

```
https://api.upsurgemedia.in/api/v1/payroll
```

## Overview

The Payroll API enables organizations to manage the complete payroll lifecycle from salary structures and calculations to payslip generation and compliance reporting, with support for multiple pay frequencies, tax calculations, and automated processing.

## Payroll Models

```typescript
interface Payroll {
  id: string;                      // UUID
  employeeId: string;              // Employee ID
  companyId: string;               // Company ID
  payPeriod: PayPeriod;            // Pay period information
  
  // Salary Components
  basicSalary: number;             // Basic salary amount
  grossSalary: number;             // Gross salary
  netSalary: number;               // Net salary (take-home)
  
  // Earnings
  earnings: PayrollEarning[];      // Earnings breakdown
  totalEarnings: number;           // Total earnings
  
  // Deductions
  deductions: PayrollDeduction[];  // Deductions breakdown
  totalDeductions: number;         // Total deductions
  
  // Tax Information
  taxDeductions: TaxDeduction[];   // Tax deductions
  totalTax: number;                // Total tax amount
  taxYear: number;                 // Tax year
  
  // Overtime and Hours
  regularHours: number;            // Regular working hours
  overtimeHours: number;           // Overtime hours
  overtimeRate: number;            // Overtime hourly rate
  overtimePay: number;             // Total overtime pay
  
  // Attendance Impact
  workingDays: number;             // Working days in period
  actualWorkingDays: number;       // Actual attended days
  paidLeaveDays: number;           // Paid leave days
  unpaidLeaveDays: number;         // Unpaid leave days
  
  // Status and Processing
  status: PayrollStatus;           // Processing status
  processedAt?: Date;              // Processing timestamp
  processedBy?: string;            // Processed by user ID
  approvedAt?: Date;               // Approval timestamp
  approvedBy?: string;             // Approved by user ID
  
  // Bank Details
  bankAccount?: BankAccount;       // Bank account details
  paymentMethod: PaymentMethod;    // Payment method
  paymentReference?: string;       // Payment reference
  
  // Compliance
  pfAmount: number;                // Provident fund
  esiAmount: number;               // ESI amount
  professionalTax: number;         // Professional tax
  
  // Timestamps
  createdAt: Date;
  updatedAt: Date;
  
  // Relationships
  employee?: Employee;             // Employee details
  payslip?: Payslip;              // Generated payslip
  salaryStructure?: SalaryStructure; // Salary structure used
}

interface SalaryStructure {
  id: string;                      // UUID
  name: string;                    // Structure name
  description?: string;            // Structure description
  companyId: string;               // Company ID
  
  // Salary Components
  basicSalaryPercentage: number;   // Basic salary percentage
  components: SalaryComponent[];   // Salary components
  
  // Applicability
  isDefault: boolean;              // Default structure
  applicableRoles?: string[];      // Applicable job roles
  applicableDepartments?: string[]; // Applicable departments
  salaryRange: {                   // Applicable salary range
    min: number;
    max: number;
  };
  
  // Settings
  isActive: boolean;               // Structure status
  effectiveFrom: Date;             // Effective from date
  effectiveTo?: Date;              // Effective to date
  
  // Timestamps
  createdAt: Date;
  updatedAt: Date;
}

interface SalaryComponent {
  id: string;                      // Component UUID
  name: string;                    // Component name
  type: ComponentType;             // Component type
  category: ComponentCategory;     // Earning or deduction
  
  // Calculation
  calculationType: CalculationType; // Fixed, percentage, formula
  value: number;                   // Component value
  formula?: string;                // Calculation formula
  
  // Settings
  isTaxable: boolean;              // Taxable component
  isStatutory: boolean;            // Statutory component
  isVariable: boolean;             // Variable component
  maxAmount?: number;              // Maximum amount
  minAmount?: number;              // Minimum amount
  
  // Conditions
  conditions?: ComponentCondition[]; // Applicability conditions
  
  // Timestamps
  createdAt: Date;
  updatedAt: Date;
}

interface PayrollEarning {
  componentId: string;             // Component ID
  componentName: string;           // Component name
  amount: number;                  // Earning amount
  calculationBase?: number;        // Calculation base
  isTaxable: boolean;              // Taxable flag
  remarks?: string;                // Additional remarks
}

interface PayrollDeduction {
  componentId: string;             // Component ID
  componentName: string;           // Component name
  amount: number;                  // Deduction amount
  calculationBase?: number;        // Calculation base
  isStatutory: boolean;            // Statutory deduction
  remarks?: string;                // Additional remarks
}

interface TaxDeduction {
  taxType: TaxType;                // Tax type
  taxableIncome: number;           // Taxable income
  taxRate: number;                 // Tax rate
  taxAmount: number;               // Tax amount
  exemptionAmount?: number;        // Exemption amount
  rebateAmount?: number;           // Rebate amount
}

interface PayPeriod {
  startDate: Date;                 // Period start date
  endDate: Date;                   // Period end date
  month: number;                   // Month (1-12)
  year: number;                    // Year
  frequency: PayFrequency;         // Pay frequency
  payDate: Date;                   // Actual pay date
}

interface Payslip {
  id: string;                      // UUID
  payrollId: string;               // Payroll ID
  employeeId: string;              // Employee ID
  companyId: string;               // Company ID
  
  // Document Details
  payslipNumber: string;           // Payslip number
  generateDate: Date;              // Generation date
  filePath?: string;               // PDF file path
  fileUrl?: string;                // PDF download URL
  
  // Content
  template: string;                // Template used
  customData?: Record<string, any>; // Custom template data
  
  // Distribution
  isEmailSent: boolean;            // Email sent status
  emailSentAt?: Date;              // Email sent timestamp
  isDownloaded: boolean;           // Downloaded status
  downloadCount: number;           // Download count
  
  // Timestamps
  createdAt: Date;
  updatedAt: Date;
}

enum PayrollStatus {
  DRAFT = 'draft',
  CALCULATED = 'calculated',
  REVIEWED = 'reviewed',
  APPROVED = 'approved',
  PROCESSED = 'processed',
  PAID = 'paid',
  CANCELLED = 'cancelled'
}

enum ComponentType {
  BASIC_SALARY = 'basic_salary',
  ALLOWANCE = 'allowance',
  BONUS = 'bonus',
  OVERTIME = 'overtime',
  COMMISSION = 'commission',
  REIMBURSEMENT = 'reimbursement',
  TAX = 'tax',
  PF = 'pf',
  ESI = 'esi',
  PROFESSIONAL_TAX = 'professional_tax',
  LOAN_DEDUCTION = 'loan_deduction',
  OTHER = 'other'
}

enum ComponentCategory {
  EARNING = 'earning',
  DEDUCTION = 'deduction'
}

enum CalculationType {
  FIXED = 'fixed',
  PERCENTAGE = 'percentage',
  FORMULA = 'formula'
}

enum PayFrequency {
  MONTHLY = 'monthly',
  WEEKLY = 'weekly',
  BIWEEKLY = 'biweekly',
  QUARTERLY = 'quarterly',
  ANNUALLY = 'annually'
}

enum PaymentMethod {
  BANK_TRANSFER = 'bank_transfer',
  CASH = 'cash',
  CHEQUE = 'cheque',
  UPI = 'upi'
}

enum TaxType {
  INCOME_TAX = 'income_tax',
  PROFESSIONAL_TAX = 'professional_tax',
  TDS = 'tds'
}
```

## Payroll Processing

### Get Payroll Records

Retrieve payroll records with filtering and pagination.

**Endpoint:** `GET /payroll`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `employeeId` (string): Filter by employee
- `month` (number): Filter by month (1-12)
- `year` (number): Filter by year
- `status` (string): Filter by status
- `departmentId` (string): Filter by department
- `payPeriodStart` (string): Pay period start date
- `payPeriodEnd` (string): Pay period end date
- `page` (number): Page number
- `limit` (number): Items per page

**Response:**
```json
{
  "data": [
    {
      "id": "payroll-uuid-1",
      "employeeId": "emp-uuid-1",
      "employee": {
        "employeeId": "EMP001",
        "user": {
          "fullName": "John Doe",
          "email": "john.doe@upsurgemedia.com"
        },
        "jobTitle": "Software Engineer",
        "department": {
          "name": "Engineering"
        }
      },
      "payPeriod": {
        "startDate": "2025-09-01",
        "endDate": "2025-09-30",
        "month": 9,
        "year": 2025,
        "frequency": "monthly",
        "payDate": "2025-10-01"
      },
      "basicSalary": 50000,
      "grossSalary": 65000,
      "netSalary": 52500,
      "totalEarnings": 65000,
      "totalDeductions": 12500,
      "totalTax": 8500,
      "regularHours": 176,
      "overtimeHours": 8,
      "overtimePay": 3000,
      "workingDays": 22,
      "actualWorkingDays": 21,
      "paidLeaveDays": 1,
      "unpaidLeaveDays": 0,
      "status": "approved",
      "processedAt": "2025-09-30T18:00:00Z",
      "approvedAt": "2025-10-01T09:00:00Z",
      "paymentMethod": "bank_transfer",
      "pfAmount": 1800,
      "esiAmount": 488,
      "professionalTax": 200,
      "earnings": [
        {
          "componentName": "Basic Salary",
          "amount": 50000,
          "isTaxable": true
        },
        {
          "componentName": "House Rent Allowance",
          "amount": 12000,
          "isTaxable": true
        },
        {
          "componentName": "Overtime Pay",
          "amount": 3000,
          "isTaxable": true
        }
      ],
      "deductions": [
        {
          "componentName": "Provident Fund",
          "amount": 1800,
          "isStatutory": true
        },
        {
          "componentName": "ESI",
          "amount": 488,
          "isStatutory": true
        },
        {
          "componentName": "Income Tax",
          "amount": 8500,
          "isStatutory": true
        },
        {
          "componentName": "Professional Tax",
          "amount": 200,
          "isStatutory": true
        }
      ]
    }
  ],
  "total": 250,
  "page": 1,
  "limit": 10,
  "summary": {
    "totalPayroll": 250,
    "pendingApproval": 5,
    "processed": 240,
    "totalPayrollAmount": 13750000,
    "avgSalary": 55000
  }
}
```

**Example:**
```bash
curl -X GET "https://api.upsurgemedia.in/api/v1/payroll?month=9&year=2025&status=approved" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

### Get Payroll by ID

Retrieve detailed payroll information for a specific record.

**Endpoint:** `GET /payroll/{id}`

**Headers:** `Authorization: Bearer <token>`

**Response:**
```json
{
  "data": {
    "id": "payroll-uuid-1",
    "employeeId": "emp-uuid-1",
    "employee": {
      "employeeId": "EMP001",
      "user": {
        "fullName": "John Doe",
        "email": "john.doe@upsurgemedia.com",
        "phone": "+1234567890"
      },
      "jobTitle": "Software Engineer",
      "department": {
        "name": "Engineering",
        "head": "Sarah Johnson"
      },
      "joiningDate": "2024-01-15",
      "employmentType": "full_time"
    },
    "payPeriod": {
      "startDate": "2025-09-01",
      "endDate": "2025-09-30",
      "month": 9,
      "year": 2025,
      "frequency": "monthly",
      "payDate": "2025-10-01"
    },
    "salaryStructure": {
      "id": "structure-uuid",
      "name": "Software Engineer - Level 1",
      "basicSalaryPercentage": 50
    },
    "basicSalary": 50000,
    "grossSalary": 65000,
    "netSalary": 52500,
    "totalEarnings": 65000,
    "totalDeductions": 12500,
    "totalTax": 8500,
    "regularHours": 176,
    "overtimeHours": 8,
    "overtimeRate": 375,
    "overtimePay": 3000,
    "workingDays": 22,
    "actualWorkingDays": 21,
    "paidLeaveDays": 1,
    "unpaidLeaveDays": 0,
    "status": "approved",
    "processedAt": "2025-09-30T18:00:00Z",
    "processedBy": "hr-user-uuid",
    "approvedAt": "2025-10-01T09:00:00Z",
    "approvedBy": "manager-user-uuid",
    "paymentMethod": "bank_transfer",
    "paymentReference": "PAY-2025-09-001",
    "bankAccount": {
      "accountNumber": "****5678",
      "bankName": "Example Bank",
      "ifscCode": "EXAM0001234"
    },
    "earnings": [
      {
        "componentId": "comp-basic",
        "componentName": "Basic Salary",
        "amount": 50000,
        "calculationBase": 50000,
        "isTaxable": true
      },
      {
        "componentId": "comp-hra",
        "componentName": "House Rent Allowance",
        "amount": 12000,
        "calculationBase": 50000,
        "isTaxable": true,
        "remarks": "24% of basic salary"
      },
      {
        "componentId": "comp-overtime",
        "componentName": "Overtime Pay",
        "amount": 3000,
        "calculationBase": 8,
        "isTaxable": true,
        "remarks": "8 hours @ ₹375/hour"
      }
    ],
    "deductions": [
      {
        "componentId": "comp-pf",
        "componentName": "Provident Fund",
        "amount": 1800,
        "calculationBase": 50000,
        "isStatutory": true,
        "remarks": "12% of basic salary (Employee: 12%, Employer: 12%)"
      },
      {
        "componentId": "comp-esi",
        "componentName": "ESI",
        "amount": 488,
        "calculationBase": 65000,
        "isStatutory": true,
        "remarks": "0.75% of gross salary"
      },
      {
        "componentId": "comp-tax",
        "componentName": "Income Tax (TDS)",
        "amount": 8500,
        "calculationBase": 65000,
        "isStatutory": true,
        "remarks": "As per IT slab rates"
      },
      {
        "componentId": "comp-pt",
        "componentName": "Professional Tax",
        "amount": 200,
        "calculationBase": 65000,
        "isStatutory": true,
        "remarks": "State professional tax"
      }
    ],
    "taxDeductions": [
      {
        "taxType": "income_tax",
        "taxableIncome": 65000,
        "taxRate": 20,
        "taxAmount": 8500,
        "exemptionAmount": 2500,
        "rebateAmount": 0
      }
    ],
    "pfAmount": 1800,
    "esiAmount": 488,
    "professionalTax": 200,
    "payslip": {
      "id": "payslip-uuid",
      "payslipNumber": "PS-2025-09-001",
      "generateDate": "2025-10-01T10:00:00Z",
      "fileUrl": "/payroll/payslips/PS-2025-09-001.pdf",
      "isEmailSent": true,
      "downloadCount": 3
    },
    "createdAt": "2025-09-30T18:00:00Z",
    "updatedAt": "2025-10-01T09:00:00Z"
  }
}
```

### Calculate Payroll

Calculate payroll for specific employees or all employees for a pay period.

**Endpoint:** `POST /payroll/calculate`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "payPeriod": {
    "startDate": "2025-10-01",
    "endDate": "2025-10-31",
    "payDate": "2025-11-01"
  },
  "employeeIds": ["emp-uuid-1", "emp-uuid-2"],
  "includeOvertime": true,
  "includeAttendance": true,
  "autoApprove": false,
  "generatePayslips": false
}
```

**Response:**
```json
{
  "data": {
    "calculationId": "calc-uuid",
    "payPeriod": {
      "startDate": "2025-10-01",
      "endDate": "2025-10-31",
      "month": 10,
      "year": 2025,
      "payDate": "2025-11-01"
    },
    "calculatedPayrolls": [
      {
        "employeeId": "emp-uuid-1",
        "payrollId": "new-payroll-uuid-1",
        "grossSalary": 65000,
        "netSalary": 52500,
        "status": "calculated"
      },
      {
        "employeeId": "emp-uuid-2", 
        "payrollId": "new-payroll-uuid-2",
        "grossSalary": 48000,
        "netSalary": 39500,
        "status": "calculated"
      }
    ],
    "summary": {
      "totalEmployees": 2,
      "successfulCalculations": 2,
      "failedCalculations": 0,
      "totalGrossAmount": 113000,
      "totalNetAmount": 92000,
      "totalDeductions": 21000
    },
    "calculatedAt": "2025-10-01T10:00:00Z"
  },
  "message": "Payroll calculated successfully for 2 employees"
}
```

### Approve Payroll

Approve calculated payroll records.

**Endpoint:** `POST /payroll/{id}/approve`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "approvalNotes": "Payroll reviewed and approved. All calculations verified.",
  "generatePayslips": true,
  "sendEmailNotifications": true
}
```

**Response:**
```json
{
  "data": {
    "payrollId": "payroll-uuid",
    "status": "approved",
    "approvedBy": "manager-uuid",
    "approvedAt": "2025-10-01T14:00:00Z",
    "approvalNotes": "Payroll reviewed and approved. All calculations verified.",
    "payslipGenerated": true,
    "emailNotificationSent": true
  },
  "message": "Payroll approved successfully"
}
```

### Process Payroll Batch

Approve and process multiple payroll records in batch.

**Endpoint:** `POST /payroll/process-batch`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "payrollIds": ["payroll-uuid-1", "payroll-uuid-2", "payroll-uuid-3"],
  "approvalNotes": "Batch approval for October 2025 payroll",
  "generatePayslips": true,
  "sendEmailNotifications": true,
  "processPayments": false
}
```

**Response:**
```json
{
  "data": {
    "batchId": "batch-uuid",
    "totalPayrolls": 3,
    "approvedPayrolls": 3,
    "failedApprovals": 0,
    "totalAmount": 195000,
    "payslipsGenerated": 3,
    "emailsSent": 3,
    "processedAt": "2025-10-01T15:00:00Z"
  },
  "message": "Batch payroll processing completed successfully"
}
```

## Salary Structures

### Get Salary Structures

Retrieve salary structures for the company.

**Endpoint:** `GET /payroll/salary-structures`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `isActive` (boolean): Filter by active status
- `isDefault` (boolean): Filter default structures

**Response:**
```json
{
  "data": [
    {
      "id": "structure-uuid-1",
      "name": "Software Engineer - Level 1",
      "description": "Salary structure for entry-level software engineers",
      "basicSalaryPercentage": 50,
      "isDefault": true,
      "isActive": true,
      "applicableRoles": ["Software Engineer", "Junior Developer"],
      "salaryRange": {
        "min": 40000,
        "max": 70000
      },
      "effectiveFrom": "2025-01-01",
      "components": [
        {
          "id": "comp-basic",
          "name": "Basic Salary",
          "type": "basic_salary",
          "category": "earning",
          "calculationType": "percentage",
          "value": 50,
          "isTaxable": true,
          "isStatutory": false
        },
        {
          "id": "comp-hra",
          "name": "House Rent Allowance",
          "type": "allowance",
          "category": "earning",
          "calculationType": "percentage",
          "value": 24,
          "isTaxable": true,
          "isStatutory": false,
          "maxAmount": 15000
        },
        {
          "id": "comp-pf",
          "name": "Provident Fund",
          "type": "pf",
          "category": "deduction",
          "calculationType": "percentage",
          "value": 12,
          "isTaxable": false,
          "isStatutory": true
        }
      ],
      "createdAt": "2025-01-01T00:00:00Z"
    }
  ],
  "total": 5
}
```

### Create Salary Structure

Create a new salary structure.

**Endpoint:** `POST /payroll/salary-structures`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "name": "Senior Developer - Level 2",
  "description": "Salary structure for senior developers",
  "basicSalaryPercentage": 45,
  "isDefault": false,
  "applicableRoles": ["Senior Developer", "Tech Lead"],
  "applicableDepartments": ["Engineering", "Product"],
  "salaryRange": {
    "min": 80000,
    "max": 120000
  },
  "effectiveFrom": "2025-11-01",
  "components": [
    {
      "name": "Basic Salary",
      "type": "basic_salary",
      "category": "earning",
      "calculationType": "percentage",
      "value": 45,
      "isTaxable": true,
      "isStatutory": false
    },
    {
      "name": "House Rent Allowance",
      "type": "allowance",
      "category": "earning",
      "calculationType": "percentage",
      "value": 20,
      "isTaxable": true,
      "maxAmount": 25000
    },
    {
      "name": "Special Allowance",
      "type": "allowance",
      "category": "earning",
      "calculationType": "percentage",
      "value": 35,
      "isTaxable": true
    },
    {
      "name": "Provident Fund",
      "type": "pf",
      "category": "deduction",
      "calculationType": "percentage",
      "value": 12,
      "isTaxable": false,
      "isStatutory": true
    },
    {
      "name": "Professional Tax",
      "type": "professional_tax",
      "category": "deduction",
      "calculationType": "fixed",
      "value": 200,
      "isTaxable": false,
      "isStatutory": true
    }
  ]
}
```

**Response:**
```json
{
  "data": {
    "id": "new-structure-uuid",
    "name": "Senior Developer - Level 2",
    "basicSalaryPercentage": 45,
    "isActive": true,
    "componentsCount": 5,
    "createdAt": "2025-10-01T16:00:00Z"
  },
  "message": "Salary structure created successfully"
}
```

## Payslip Management

### Get Payslips

Retrieve payslips for employees.

**Endpoint:** `GET /payroll/payslips`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `employeeId` (string): Filter by employee
- `month` (number): Filter by month
- `year` (number): Filter by year
- `status` (string): Filter by status

**Response:**
```json
{
  "data": [
    {
      "id": "payslip-uuid-1",
      "payrollId": "payroll-uuid-1",
      "payslipNumber": "PS-2025-09-001",
      "employee": {
        "employeeId": "EMP001",
        "user": {
          "fullName": "John Doe"
        }
      },
      "payPeriod": {
        "month": 9,
        "year": 2025,
        "startDate": "2025-09-01",
        "endDate": "2025-09-30"
      },
      "grossSalary": 65000,
      "netSalary": 52500,
      "generateDate": "2025-10-01T10:00:00Z",
      "fileUrl": "/payroll/payslips/PS-2025-09-001.pdf",
      "isEmailSent": true,
      "emailSentAt": "2025-10-01T10:30:00Z",
      "downloadCount": 3,
      "template": "standard_payslip"
    }
  ],
  "total": 125
}
```

### Generate Payslip

Generate payslip for a specific payroll record.

**Endpoint:** `POST /payroll/{payrollId}/generate-payslip`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "template": "standard_payslip",
  "sendEmail": true,
  "emailSubject": "Payslip for September 2025",
  "emailMessage": "Please find attached your payslip for September 2025.",
  "customData": {
    "companyLogo": "https://assets.upsurgemedia.in/logo.png",
    "companyAddress": "123 Business Street, City, State 12345",
    "hrContactEmail": "hr@upsurgemedia.com"
  }
}
```

**Response:**
```json
{
  "data": {
    "payslipId": "new-payslip-uuid",
    "payslipNumber": "PS-2025-09-002",
    "fileUrl": "/payroll/payslips/PS-2025-09-002.pdf",
    "emailSent": true,
    "emailSentAt": "2025-10-01T17:00:00Z",
    "generatedAt": "2025-10-01T17:00:00Z"
  },
  "message": "Payslip generated and emailed successfully"
}
```

### Download Payslip

Download payslip PDF file.

**Endpoint:** `GET /payroll/payslips/{id}/download`

**Headers:** `Authorization: Bearer <token>`

**Response:** PDF file download

**Example:**
```bash
curl -X GET "https://api.upsurgemedia.in/api/v1/payroll/payslips/payslip-uuid/download" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -o "payslip.pdf"
```

### Email Payslip

Send payslip via email to employee.

**Endpoint:** `POST /payroll/payslips/{id}/email`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "emailSubject": "Your Payslip for September 2025",
  "emailMessage": "Dear Employee,\n\nPlease find attached your payslip for September 2025.\n\nBest regards,\nHR Team",
  "ccEmails": ["manager@upsurgemedia.com"],
  "bccEmails": ["hr@upsurgemedia.com"]
}
```

**Response:**
```json
{
  "data": {
    "payslipId": "payslip-uuid",
    "emailSent": true,
    "emailSentAt": "2025-10-01T18:00:00Z",
    "recipientEmail": "john.doe@upsurgemedia.com"
  },
  "message": "Payslip emailed successfully"
}
```

## Payroll Reports

### Get Payroll Summary

Get payroll summary and statistics.

**Endpoint:** `GET /payroll/summary`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `month` (number): Filter by month
- `year` (number): Filter by year
- `departmentId` (string): Filter by department

**Response:**
```json
{
  "data": {
    "period": {
      "month": 9,
      "year": 2025,
      "startDate": "2025-09-01",
      "endDate": "2025-09-30"
    },
    "overview": {
      "totalEmployees": 250,
      "processedPayrolls": 245,
      "pendingPayrolls": 5,
      "totalGrossAmount": 13750000,
      "totalNetAmount": 11000000,
      "totalDeductions": 2750000,
      "avgGrossSalary": 55000,
      "avgNetSalary": 44000
    },
    "breakdowns": {
      "byDepartment": [
        {
          "departmentName": "Engineering",
          "employees": 85,
          "totalGross": 5100000,
          "totalNet": 4080000,
          "avgSalary": 60000
        },
        {
          "departmentName": "Sales",
          "employees": 45,
          "totalGross": 1800000,
          "totalNet": 1440000,
          "avgSalary": 40000
        }
      ],
      "byComponents": [
        {
          "componentName": "Basic Salary",
          "totalAmount": 6875000,
          "avgAmount": 27500
        },
        {
          "componentName": "House Rent Allowance",
          "totalAmount": 2062500,
          "avgAmount": 8250
        }
      ],
      "byDeductions": [
        {
          "componentName": "Income Tax",
          "totalAmount": 1375000,
          "avgAmount": 5500
        },
        {
          "componentName": "Provident Fund",
          "totalAmount": 825000,
          "avgAmount": 3300
        }
      ]
    },
    "compliance": {
      "totalPF": 825000,
      "totalESI": 206250,
      "totalProfessionalTax": 50000,
      "totalTDS": 1375000
    },
    "trends": {
      "payrollGrowth": 5.2,
      "avgSalaryGrowth": 3.8,
      "deductionPercentage": 20.0
    }
  }
}
```

### Generate Payroll Report

Generate comprehensive payroll reports.

**Endpoint:** `POST /payroll/reports/generate`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "reportType": "monthly_summary",
  "period": {
    "startDate": "2025-09-01",
    "endDate": "2025-09-30"
  },
  "filters": {
    "departmentIds": ["dept-uuid-1", "dept-uuid-2"],
    "employeeIds": [],
    "salaryRange": {
      "min": 30000,
      "max": 100000
    }
  },
  "format": "pdf",
  "includeCharts": true,
  "emailTo": ["manager@upsurgemedia.com", "hr@upsurgemedia.com"]
}
```

**Response:**
```json
{
  "data": {
    "reportId": "report-uuid",
    "reportType": "monthly_summary",
    "fileUrl": "/payroll/reports/monthly_summary_2025_09.pdf",
    "generatedAt": "2025-10-01T19:00:00Z",
    "recordCount": 245,
    "totalAmount": 13750000,
    "emailSent": true
  },
  "message": "Payroll report generated successfully"
}
```

## Integration Examples

### JavaScript/React Payroll Management

```javascript
class PayrollService {
  constructor(baseURL = 'https://api.upsurgemedia.in') {
    this.api = axios.create({ baseURL });
  }

  setToken(token) {
    this.api.defaults.headers.common['Authorization'] = `Bearer ${token}`;
  }

  async getPayrollRecords(params = {}) {
    const response = await this.api.get('/payroll', { params });
    return response.data;
  }

  async calculatePayroll(calculationData) {
    const response = await this.api.post('/payroll/calculate', calculationData);
    return response.data.data;
  }

  async approvePayroll(payrollId, approvalData) {
    const response = await this.api.post(`/payroll/${payrollId}/approve`, approvalData);
    return response.data.data;
  }

  async processBatch(batchData) {
    const response = await this.api.post('/payroll/process-batch', batchData);
    return response.data.data;
  }

  async getSalaryStructures(params = {}) {
    const response = await this.api.get('/payroll/salary-structures', { params });
    return response.data.data;
  }

  async generatePayslip(payrollId, payslipData) {
    const response = await this.api.post(`/payroll/${payrollId}/generate-payslip`, payslipData);
    return response.data.data;
  }

  async downloadPayslip(payslipId) {
    const response = await this.api.get(`/payroll/payslips/${payslipId}/download`, {
      responseType: 'blob'
    });
    
    // Create download link
    const url = window.URL.createObjectURL(new Blob([response.data]));
    const link = document.createElement('a');
    link.href = url;
    link.setAttribute('download', `payslip_${payslipId}.pdf`);
    document.body.appendChild(link);
    link.click();
    link.remove();
    
    return response;
  }

  async getPayrollSummary(params = {}) {
    const response = await this.api.get('/payroll/summary', { params });
    return response.data.data;
  }

  async generateReport(reportData) {
    const response = await this.api.post('/payroll/reports/generate', reportData);
    return response.data.data;
  }
}

// Usage
const payrollService = new PayrollService();
payrollService.setToken('your-jwt-token');

// Calculate payroll for October 2025
const calculation = await payrollService.calculatePayroll({
  payPeriod: {
    startDate: '2025-10-01',
    endDate: '2025-10-31',
    payDate: '2025-11-01'
  },
  includeOvertime: true,
  includeAttendance: true,
  autoApprove: false
});

console.log(`Calculated payroll for ${calculation.totalEmployees} employees`);
console.log(`Total gross amount: ₹${calculation.summary.totalGrossAmount}`);

// Approve payroll
for (const payroll of calculation.calculatedPayrolls) {
  await payrollService.approvePayroll(payroll.payrollId, {
    approvalNotes: 'Reviewed and approved',
    generatePayslips: true,
    sendEmailNotifications: true
  });
}

// Get payroll summary
const summary = await payrollService.getPayrollSummary({
  month: 10,
  year: 2025
});

console.log('Payroll Summary:');
console.log(`Total employees: ${summary.overview.totalEmployees}`);
console.log(`Total gross: ₹${summary.overview.totalGrossAmount}`);
console.log(`Average salary: ₹${summary.overview.avgGrossSalary}`);
```

### Python Payroll Automation

```python
import requests
from typing import Dict, List, Optional
from datetime import datetime, timedelta

class PayrollAPI:
    def __init__(self, base_url: str = "https://api.upsurgemedia.in"):
        self.base_url = base_url
        self.session = requests.Session()

    def set_token(self, token: str):
        self.session.headers.update({"Authorization": f"Bearer {token}"})

    def calculate_payroll(self, pay_period: Dict, employee_ids: List[str] = None) -> Dict:
        payload = {
            "payPeriod": pay_period,
            "includeOvertime": True,
            "includeAttendance": True,
            "autoApprove": False,
            "generatePayslips": False
        }
        
        if employee_ids:
            payload["employeeIds"] = employee_ids
            
        response = self.session.post(f"{self.base_url}/payroll/calculate", json=payload)
        response.raise_for_status()
        return response.json()["data"]

    def approve_batch_payroll(self, payroll_ids: List[str], 
                            approval_notes: str = "Batch approval") -> Dict:
        payload = {
            "payrollIds": payroll_ids,
            "approvalNotes": approval_notes,
            "generatePayslips": True,
            "sendEmailNotifications": True,
            "processPayments": False
        }
        
        response = self.session.post(f"{self.base_url}/payroll/process-batch", json=payload)
        response.raise_for_status()
        return response.json()["data"]

    def get_payroll_summary(self, month: int, year: int, 
                          department_id: str = None) -> Dict:
        params = {"month": month, "year": year}
        if department_id:
            params["departmentId"] = department_id
            
        response = self.session.get(f"{self.base_url}/payroll/summary", params=params)
        response.raise_for_status()
        return response.json()["data"]

    def generate_payroll_report(self, report_config: Dict) -> Dict:
        response = self.session.post(
            f"{self.base_url}/payroll/reports/generate",
            json=report_config
        )
        response.raise_for_status()
        return response.json()["data"]

    def create_salary_structure(self, structure_data: Dict) -> Dict:
        response = self.session.post(
            f"{self.base_url}/payroll/salary-structures",
            json=structure_data
        )
        response.raise_for_status()
        return response.json()["data"]

    # Automated payroll processing workflow
    def process_monthly_payroll(self, month: int, year: int) -> Dict:
        """Complete automated payroll processing workflow"""
        
        # Calculate pay period
        start_date = f"{year}-{month:02d}-01"
        
        # Calculate last day of month
        if month == 12:
            next_month = datetime(year + 1, 1, 1)
        else:
            next_month = datetime(year, month + 1, 1)
        last_day = next_month - timedelta(days=1)
        end_date = last_day.strftime("%Y-%m-%d")
        
        # Calculate next month's first day for pay date
        pay_date = next_month.strftime("%Y-%m-%d")
        
        pay_period = {
            "startDate": start_date,
            "endDate": end_date,
            "payDate": pay_date
        }
        
        print(f"Starting payroll processing for {month}/{year}")
        print(f"Pay period: {start_date} to {end_date}")
        
        # Step 1: Calculate payroll
        calculation = self.calculate_payroll(pay_period)
        print(f"Calculated payroll for {calculation['summary']['totalEmployees']} employees")
        
        # Step 2: Get calculated payroll IDs
        payroll_ids = [p["payrollId"] for p in calculation["calculatedPayrolls"]]
        
        # Step 3: Approve batch
        approval = self.approve_batch_payroll(
            payroll_ids,
            f"Automated batch approval for {month}/{year}"
        )
        print(f"Approved {approval['approvedPayrolls']} payroll records")
        
        # Step 4: Generate summary report
        summary = self.get_payroll_summary(month, year)
        
        # Step 5: Generate detailed report
        report = self.generate_payroll_report({
            "reportType": "monthly_summary",
            "period": pay_period,
            "format": "pdf",
            "includeCharts": True,
            "emailTo": ["hr@upsurgemedia.com", "finance@upsurgemedia.com"]
        })
        
        return {
            "calculation": calculation,
            "approval": approval,
            "summary": summary,
            "report": report,
            "status": "completed"
        }

    def analyze_payroll_trends(self, months: int = 6) -> Dict:
        """Analyze payroll trends over multiple months"""
        
        current_date = datetime.now()
        trends = []
        
        for i in range(months):
            # Calculate month and year
            month_date = current_date - timedelta(days=30 * i)
            month = month_date.month
            year = month_date.year
            
            try:
                summary = self.get_payroll_summary(month, year)
                trends.append({
                    "month": month,
                    "year": year,
                    "totalEmployees": summary["overview"]["totalEmployees"],
                    "totalGrossAmount": summary["overview"]["totalGrossAmount"],
                    "avgGrossSalary": summary["overview"]["avgGrossSalary"],
                    "totalDeductions": summary["overview"]["totalDeductions"]
                })
            except Exception as e:
                print(f"Could not get data for {month}/{year}: {e}")
                continue
        
        # Calculate trends
        if len(trends) >= 2:
            latest = trends[0]
            previous = trends[1]
            
            employee_growth = ((latest["totalEmployees"] - previous["totalEmployees"]) 
                              / previous["totalEmployees"] * 100)
            salary_growth = ((latest["avgGrossSalary"] - previous["avgGrossSalary"]) 
                            / previous["avgGrossSalary"] * 100)
            
            analysis = {
                "trends": trends,
                "analysis": {
                    "employee_growth_rate": round(employee_growth, 2),
                    "avg_salary_growth_rate": round(salary_growth, 2),
                    "total_payroll_growth": round(
                        ((latest["totalGrossAmount"] - previous["totalGrossAmount"]) 
                         / previous["totalGrossAmount"] * 100), 2
                    )
                },
                "insights": []
            }
            
            # Generate insights
            if employee_growth > 5:
                analysis["insights"].append("High employee growth rate - consider scaling HR processes")
            if salary_growth > 10:
                analysis["insights"].append("Significant salary growth - review budget planning")
            if latest["totalDeductions"] / latest["totalGrossAmount"] > 0.25:
                analysis["insights"].append("High deduction ratio - review tax optimization strategies")
                
            return analysis
        
        return {"trends": trends, "message": "Insufficient data for trend analysis"}

# Usage
api = PayrollAPI()
api.set_token("your-jwt-token")

# Process monthly payroll automatically
result = api.process_monthly_payroll(10, 2025)
print(f"Payroll processing completed: {result['status']}")
print(f"Total amount processed: ₹{result['summary']['overview']['totalGrossAmount']}")

# Analyze payroll trends
trends = api.analyze_payroll_trends(6)
print("\nPayroll Trends Analysis:")
print(f"Employee growth rate: {trends['analysis']['employee_growth_rate']}%")
print(f"Salary growth rate: {trends['analysis']['avg_salary_growth_rate']}%")

for insight in trends['analysis']['insights']:
    print(f"💡 {insight}")
```

---

The Payroll API provides comprehensive payroll management with automated calculations, approval workflows, compliance tracking, and detailed reporting for efficient payroll processing and management.
