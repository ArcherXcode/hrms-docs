# 🏢 Companies API

The Companies API provides comprehensive company management functionality for multi-tenant HRMS operations, including company creation, configuration, and settings management.

## Base Endpoint

```
https://api.upsurgemedia.in/companies
```

## Overview

The Companies API enables multi-tenant functionality where each company operates independently with its own employees, departments, and configurations. Super administrators can manage multiple companies, while company administrators manage their specific organization.

## Company Model

```typescript
interface Company {
  id: string;              // UUID
  name: string;            // Company name
  domain: string;          // Company domain (e.g., upsurgemedia.com)
  logo?: string;           // Company logo URL
  website?: string;        // Company website
  industry?: string;       // Industry type
  size?: CompanySize;      // Company size category
  description?: string;    // Company description
  
  // Contact Information
  email?: string;          // Primary contact email
  phone?: string;          // Primary contact phone
  
  // Address Information
  address?: {
    street: string;
    city: string;
    state: string;
    country: string;
    zipCode: string;
  };
  
  // Settings
  timezone: string;        // Company timezone
  currency: string;        // Default currency
  dateFormat: string;      // Date format preference
  
  // Status
  isActive: boolean;       // Company status
  createdAt: Date;         // Creation timestamp
  updatedAt: Date;         // Last update timestamp
  
  // Relationships
  users?: User[];          // Company users
  employees?: Employee[];   // Company employees
  departments?: Department[]; // Company departments
  settings?: CompanySettings; // Company-specific settings
}

enum CompanySize {
  STARTUP = 'startup',        // 1-10 employees
  SMALL = 'small',           // 11-50 employees
  MEDIUM = 'medium',         // 51-200 employees
  LARGE = 'large',           // 201-1000 employees
  ENTERPRISE = 'enterprise'   // 1000+ employees
}
```

## Company Management Endpoints

### Get All Companies

Retrieve a paginated list of companies (Super Admin only).

**Endpoint:** `GET /companies`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `page` (number): Page number (default: 1)
- `limit` (number): Items per page (default: 10, max: 100)
- `isActive` (boolean): Filter by active status
- `size` (string): Filter by company size
- `industry` (string): Filter by industry
- `search` (string): Search by name, domain, or email

**Response:**
```json
{
  "data": [
    {
      "id": "company-uuid",
      "name": "Upsurge Media",
      "domain": "upsurgemedia.com",
      "logo": "https://cdn.upsurgemedia.in/logo.png",
      "website": "https://upsurgemedia.com",
      "industry": "Technology",
      "size": "medium",
      "email": "info@upsurgemedia.com",
      "phone": "+1-555-0123",
      "address": {
        "street": "123 Tech Street",
        "city": "San Francisco",
        "state": "CA",
        "country": "USA",
        "zipCode": "94105"
      },
      "timezone": "America/Los_Angeles",
      "currency": "USD",
      "isActive": true,
      "createdAt": "2025-01-01T00:00:00Z",
      "employeeCount": 45,
      "departmentCount": 8
    }
  ],
  "total": 1,
  "page": 1,
  "limit": 10
}
```

**Example:**
```bash
curl -X GET "https://api.upsurgemedia.in/companies?isActive=true&size=medium" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

### Get Company by ID

Retrieve a specific company by ID.

**Endpoint:** `GET /companies/{id}`

**Headers:** `Authorization: Bearer <token>`

**Response:**
```json
{
  "data": {
    "id": "company-uuid",
    "name": "Upsurge Media",
    "domain": "upsurgemedia.com",
    "logo": "https://cdn.upsurgemedia.in/logo.png",
    "website": "https://upsurgemedia.com",
    "industry": "Technology",
    "size": "medium",
    "description": "Leading digital marketing and technology solutions provider",
    "email": "info@upsurgemedia.com",
    "phone": "+1-555-0123",
    "address": {
      "street": "123 Tech Street",
      "city": "San Francisco",
      "state": "CA",
      "country": "USA",
      "zipCode": "94105"
    },
    "timezone": "America/Los_Angeles",
    "currency": "USD",
    "dateFormat": "MM/DD/YYYY",
    "isActive": true,
    "createdAt": "2025-01-01T00:00:00Z",
    "updatedAt": "2025-09-01T10:00:00Z",
    "statistics": {
      "totalEmployees": 45,
      "activeEmployees": 42,
      "totalDepartments": 8,
      "monthlyGrowth": 5.2
    }
  }
}
```

### Get Current Company

Get the authenticated user's company information.

**Endpoint:** `GET /companies/current`

**Headers:** `Authorization: Bearer <token>`

**Response:**
```json
{
  "data": {
    "id": "company-uuid",
    "name": "Upsurge Media",
    "domain": "upsurgemedia.com",
    "logo": "https://cdn.upsurgemedia.in/logo.png",
    "timezone": "America/Los_Angeles",
    "currency": "USD",
    "dateFormat": "MM/DD/YYYY",
    "isActive": true,
    "settings": {
      "workingHours": {
        "start": "09:00",
        "end": "18:00"
      },
      "workingDays": ["monday", "tuesday", "wednesday", "thursday", "friday"],
      "leavePolicies": {
        "annualLeave": 20,
        "sickLeave": 10
      }
    }
  }
}
```

### Create Company

Create a new company (Super Admin only).

**Endpoint:** `POST /companies`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "name": "TechCorp Inc",
  "domain": "techcorp.com",
  "website": "https://techcorp.com",
  "industry": "Technology",
  "size": "small",
  "description": "Innovative technology solutions",
  "email": "contact@techcorp.com",
  "phone": "+1-555-9876",
  "address": {
    "street": "456 Innovation Ave",
    "city": "Austin",
    "state": "TX",
    "country": "USA",
    "zipCode": "78701"
  },
  "timezone": "America/Chicago",
  "currency": "USD",
  "dateFormat": "MM/DD/YYYY"
}
```

**Response:**
```json
{
  "data": {
    "id": "new-company-uuid",
    "name": "TechCorp Inc",
    "domain": "techcorp.com",
    "website": "https://techcorp.com",
    "industry": "Technology",
    "size": "small",
    "email": "contact@techcorp.com",
    "timezone": "America/Chicago",
    "currency": "USD",
    "isActive": true,
    "createdAt": "2025-09-03T10:00:00Z"
  },
  "message": "Company created successfully"
}
```

**Example:**
```bash
curl -X POST "https://api.upsurgemedia.in/companies" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "New Tech Company",
    "domain": "newtech.com",
    "industry": "Technology",
    "size": "startup",
    "timezone": "America/New_York",
    "currency": "USD"
  }'
```

### Update Company

Update company information (Admin+ only).

**Endpoint:** `PATCH /companies/{id}`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "name": "Upsurge Media Inc",
  "industry": "Digital Marketing",
  "size": "large",
  "description": "Leading digital marketing solutions provider",
  "phone": "+1-555-0199",
  "website": "https://upsurgemedia.com"
}
```

**Response:**
```json
{
  "data": {
    "id": "company-uuid",
    "name": "Upsurge Media Inc",
    "industry": "Digital Marketing",
    "size": "large",
    "description": "Leading digital marketing solutions provider",
    "phone": "+1-555-0199",
    "updatedAt": "2025-09-03T10:00:00Z"
  },
  "message": "Company updated successfully"
}
```

### Update Company Logo

Upload and update company logo.

**Endpoint:** `POST /companies/{id}/logo`

**Headers:** `Authorization: Bearer <token>`

**Content-Type:** `multipart/form-data`

**Request Body:**
```
file: <image-file>
```

**Response:**
```json
{
  "data": {
    "id": "company-uuid",
    "logo": "https://cdn.upsurgemedia.in/companies/company-uuid/logo.png",
    "updatedAt": "2025-09-03T10:00:00Z"
  },
  "message": "Company logo updated successfully"
}
```

**Example:**
```bash
curl -X POST "https://api.upsurgemedia.in/companies/company-uuid/logo" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -F "file=@/path/to/logo.png"
```

### Delete Company

Soft delete a company (Super Admin only).

**Endpoint:** `DELETE /companies/{id}`

**Headers:** `Authorization: Bearer <token>`

**Response:**
```json
{
  "message": "Company deleted successfully",
  "timestamp": "2025-09-03T10:00:00Z"
}
```

## Company Settings

### Get Company Settings

Retrieve company-specific settings.

**Endpoint:** `GET /companies/{id}/settings`

**Headers:** `Authorization: Bearer <token>`

**Response:**
```json
{
  "data": {
    "workingHours": {
      "start": "09:00",
      "end": "18:00",
      "timezone": "America/Los_Angeles"
    },
    "workingDays": ["monday", "tuesday", "wednesday", "thursday", "friday"],
    "leavePolicies": {
      "annualLeave": 20,
      "sickLeave": 10,
      "personalLeave": 5,
      "carryForwardLimit": 5
    },
    "attendanceSettings": {
      "requireLocation": true,
      "allowMobileCheckIn": true,
      "graceMinutes": 15,
      "autoCheckOut": "20:00"
    },
    "payrollSettings": {
      "currency": "USD",
      "payFrequency": "monthly",
      "payDay": 1,
      "overtimeRate": 1.5
    },
    "notificationSettings": {
      "emailNotifications": true,
      "pushNotifications": true,
      "smsNotifications": false
    },
    "securitySettings": {
      "passwordPolicy": {
        "minLength": 8,
        "requireUppercase": true,
        "requireNumbers": true,
        "requireSpecialChars": true
      },
      "sessionTimeout": 480,
      "maxLoginAttempts": 5
    }
  }
}
```

### Update Company Settings

Update company-specific settings (Admin+ only).

**Endpoint:** `PATCH /companies/{id}/settings`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "workingHours": {
    "start": "08:30",
    "end": "17:30"
  },
  "leavePolicies": {
    "annualLeave": 25,
    "sickLeave": 12
  },
  "attendanceSettings": {
    "graceMinutes": 10,
    "requireLocation": false
  }
}
```

**Response:**
```json
{
  "data": {
    "workingHours": {
      "start": "08:30",
      "end": "17:30",
      "timezone": "America/Los_Angeles"
    },
    "leavePolicies": {
      "annualLeave": 25,
      "sickLeave": 12,
      "personalLeave": 5
    },
    "attendanceSettings": {
      "graceMinutes": 10,
      "requireLocation": false,
      "allowMobileCheckIn": true
    }
  },
  "message": "Company settings updated successfully"
}
```

## Company Statistics

### Get Company Statistics

Get comprehensive company statistics and analytics.

**Endpoint:** `GET /companies/{id}/statistics`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `period` (string): Time period (today, week, month, quarter, year)
- `includeComparisons` (boolean): Include period-over-period comparisons

**Response:**
```json
{
  "data": {
    "overview": {
      "totalEmployees": 45,
      "activeEmployees": 42,
      "totalDepartments": 8,
      "monthlyGrowth": 5.2
    },
    "attendance": {
      "averageAttendance": 95.8,
      "onTimePercentage": 88.5,
      "lateArrivals": 12,
      "earlyDepartures": 8,
      "absentees": 3
    },
    "leave": {
      "pendingRequests": 5,
      "approvedThisMonth": 18,
      "rejectedThisMonth": 2,
      "averageLeaveDays": 1.8
    },
    "payroll": {
      "totalPayroll": 285000,
      "averageSalary": 6333.33,
      "overtime": 12500,
      "bonuses": 15000
    },
    "productivity": {
      "tasksCompleted": 156,
      "averageTaskTime": 4.2,
      "projectsOnTrack": 8,
      "projectsDelayed": 2
    },
    "demographics": {
      "averageAge": 32.5,
      "genderDistribution": {
        "male": 26,
        "female": 18,
        "other": 1
      },
      "departmentDistribution": {
        "Engineering": 15,
        "Marketing": 8,
        "Sales": 12,
        "HR": 3,
        "Finance": 4,
        "Operations": 3
      }
    }
  }
}
```

### Get Company Dashboard

Get dashboard data for company overview.

**Endpoint:** `GET /companies/{id}/dashboard`

**Headers:** `Authorization: Bearer <token>`

**Response:**
```json
{
  "data": {
    "todayStats": {
      "presentEmployees": 38,
      "absentEmployees": 4,
      "lateArrivals": 3,
      "workingHours": 8.5
    },
    "weeklyTrends": {
      "attendance": [95, 92, 98, 89, 94],
      "productivity": [87, 91, 85, 92, 88],
      "overtime": [2.5, 3.1, 1.8, 4.2, 2.9]
    },
    "upcomingEvents": [
      {
        "id": "event-1",
        "title": "All Hands Meeting",
        "date": "2025-09-05T10:00:00Z",
        "type": "meeting"
      },
      {
        "id": "event-2",
        "title": "John Doe - Annual Review",
        "date": "2025-09-06T14:00:00Z",
        "type": "review"
      }
    ],
    "recentActivities": [
      {
        "id": "activity-1",
        "message": "3 new employees joined",
        "timestamp": "2025-09-03T09:00:00Z",
        "type": "employee"
      },
      {
        "id": "activity-2",
        "message": "Payroll processed for August",
        "timestamp": "2025-09-01T08:00:00Z",
        "type": "payroll"
      }
    ]
  }
}
```

## Company Users

### Get Company Users

Get all users associated with a company.

**Endpoint:** `GET /companies/{id}/users`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `page` (number): Page number
- `limit` (number): Items per page
- `role` (string): Filter by role
- `isActive` (boolean): Filter by active status

**Response:**
```json
{
  "data": [
    {
      "id": "user-uuid",
      "email": "john.doe@upsurgemedia.com",
      "fullName": "John Doe",
      "role": "employee",
      "isActive": true,
      "lastLoginAt": "2025-09-03T09:30:00Z",
      "employee": {
        "employeeId": "EMP001",
        "jobTitle": "Software Developer",
        "department": "Engineering"
      }
    }
  ],
  "total": 45,
  "page": 1,
  "limit": 10
}
```

### Add User to Company

Add an existing user to a company (Super Admin only).

**Endpoint:** `POST /companies/{id}/users`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "userId": "user-uuid",
  "role": "employee"
}
```

**Response:**
```json
{
  "data": {
    "userId": "user-uuid",
    "companyId": "company-uuid",
    "role": "employee",
    "addedAt": "2025-09-03T10:00:00Z"
  },
  "message": "User added to company successfully"
}
```

## Industry and Size Options

### Get Industries

Get list of available industry options.

**Endpoint:** `GET /companies/industries`

**Response:**
```json
{
  "data": [
    {
      "value": "technology",
      "label": "Technology"
    },
    {
      "value": "healthcare",
      "label": "Healthcare"
    },
    {
      "value": "finance",
      "label": "Finance"
    },
    {
      "value": "education",
      "label": "Education"
    },
    {
      "value": "retail",
      "label": "Retail"
    },
    {
      "value": "manufacturing",
      "label": "Manufacturing"
    },
    {
      "value": "consulting",
      "label": "Consulting"
    },
    {
      "value": "other",
      "label": "Other"
    }
  ]
}
```

### Get Company Sizes

Get list of available company size options.

**Endpoint:** `GET /companies/sizes`

**Response:**
```json
{
  "data": [
    {
      "value": "startup",
      "label": "Startup (1-10)",
      "min": 1,
      "max": 10
    },
    {
      "value": "small",
      "label": "Small (11-50)",
      "min": 11,
      "max": 50
    },
    {
      "value": "medium",
      "label": "Medium (51-200)",
      "min": 51,
      "max": 200
    },
    {
      "value": "large",
      "label": "Large (201-1000)",
      "min": 201,
      "max": 1000
    },
    {
      "value": "enterprise",
      "label": "Enterprise (1000+)",
      "min": 1000,
      "max": null
    }
  ]
}
```

## Error Responses

### Common Error Codes

#### 400 Bad Request
```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Domain already exists",
    "statusCode": 400
  }
}
```

#### 403 Forbidden
```json
{
  "error": {
    "code": "FORBIDDEN", 
    "message": "Only super administrators can create companies",
    "statusCode": 403
  }
}
```

#### 404 Not Found
```json
{
  "error": {
    "code": "COMPANY_NOT_FOUND",
    "message": "Company with ID 'uuid' not found",
    "statusCode": 404
  }
}
```

#### 422 Validation Error
```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Domain format is invalid",
    "details": {
      "field": "domain",
      "value": "invalid-domain"
    },
    "statusCode": 422
  }
}
```

## Integration Examples

### JavaScript/React

```javascript
class CompanyService {
  constructor(baseURL = 'https://api.upsurgemedia.in') {
    this.api = axios.create({ baseURL });
  }

  setToken(token) {
    this.api.defaults.headers.common['Authorization'] = `Bearer ${token}`;
  }

  async getCompanies(params = {}) {
    const response = await this.api.get('/companies', { params });
    return response.data;
  }

  async getCompany(id) {
    const response = await this.api.get(`/companies/${id}`);
    return response.data.data;
  }

  async getCurrentCompany() {
    const response = await this.api.get('/companies/current');
    return response.data.data;
  }

  async createCompany(companyData) {
    const response = await this.api.post('/companies', companyData);
    return response.data.data;
  }

  async updateCompany(id, updates) {
    const response = await this.api.patch(`/companies/${id}`, updates);
    return response.data.data;
  }

  async uploadLogo(id, logoFile) {
    const formData = new FormData();
    formData.append('file', logoFile);
    
    const response = await this.api.post(`/companies/${id}/logo`, formData, {
      headers: { 'Content-Type': 'multipart/form-data' }
    });
    return response.data.data;
  }

  async getCompanySettings(id) {
    const response = await this.api.get(`/companies/${id}/settings`);
    return response.data.data;
  }

  async updateCompanySettings(id, settings) {
    const response = await this.api.patch(`/companies/${id}/settings`, settings);
    return response.data.data;
  }

  async getCompanyStatistics(id, params = {}) {
    const response = await this.api.get(`/companies/${id}/statistics`, { params });
    return response.data.data;
  }
}

// Usage
const companyService = new CompanyService();
companyService.setToken('your-jwt-token');

// Get current company
const company = await companyService.getCurrentCompany();
console.log('Company:', company.name);

// Update company settings
const updatedSettings = await companyService.updateCompanySettings(company.id, {
  workingHours: { start: '08:00', end: '17:00' },
  leavePolicies: { annualLeave: 25 }
});
```

### Python

```python
import requests
from typing import Dict, Optional

class CompanyAPI:
    def __init__(self, base_url: str = "https://api.upsurgemedia.in"):
        self.base_url = base_url
        self.session = requests.Session()

    def set_token(self, token: str):
        self.session.headers.update({"Authorization": f"Bearer {token}"})

    def get_companies(self, **params) -> Dict:
        response = self.session.get(f"{self.base_url}/companies", params=params)
        response.raise_for_status()
        return response.json()

    def get_company(self, company_id: str) -> Dict:
        response = self.session.get(f"{self.base_url}/companies/{company_id}")
        response.raise_for_status()
        return response.json()["data"]

    def get_current_company(self) -> Dict:
        response = self.session.get(f"{self.base_url}/companies/current")
        response.raise_for_status()
        return response.json()["data"]

    def create_company(self, company_data: Dict) -> Dict:
        response = self.session.post(
            f"{self.base_url}/companies",
            json=company_data,
            headers={"Content-Type": "application/json"}
        )
        response.raise_for_status()
        return response.json()["data"]

    def update_company(self, company_id: str, updates: Dict) -> Dict:
        response = self.session.patch(
            f"{self.base_url}/companies/{company_id}",
            json=updates,
            headers={"Content-Type": "application/json"}
        )
        response.raise_for_status()
        return response.json()["data"]

    def get_company_statistics(self, company_id: str, **params) -> Dict:
        response = self.session.get(
            f"{self.base_url}/companies/{company_id}/statistics",
            params=params
        )
        response.raise_for_status()
        return response.json()["data"]

# Usage
api = CompanyAPI()
api.set_token("your-jwt-token")

# Get current company
company = api.get_current_company()
print(f"Company: {company['name']} ({company['size']})")

# Get company statistics
stats = api.get_company_statistics(company['id'], period='month')
print(f"Total employees: {stats['overview']['totalEmployees']}")
```

---

The Companies API provides comprehensive multi-tenant company management with extensive configuration options, statistics, and settings management for enterprise HRMS operations.
