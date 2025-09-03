# 🏗️ API Overview

The Upsurge HRMS API is a comprehensive RESTful API that provides complete human resource management functionality. Built with NestJS and TypeScript, it offers robust, scalable, and secure endpoints for all HR operations.

## 🌐 Base Information

- **Base URL**: `https://api.upsurgemedia.in`
- **API Version**: v1
- **Protocol**: HTTPS only
- **Authentication**: JWT Bearer Token
- **Data Format**: JSON
- **Swagger Documentation**: `https://api.upsurgemedia.in/api`

## 📊 API Categories

Our API is organized into logical categories for easy navigation and understanding:

### 🔐 Core APIs

**Authentication & Authorization**
- User login/logout
- Token refresh
- Role-based access control
- Session management

**User Management**
- User CRUD operations
- Profile management
- Role assignments
- Password management

**Company Management**
- Multi-tenant company support
- Company settings
- Branding configuration
- Company-wide policies

### ⏰ Attendance & Time Management

**Attendance Tracking**
- Check-in/check-out
- Break management
- Location-based attendance
- Attendance history

**Time Tracking**
- Work hours calculation
- Overtime tracking
- Time sheets
- Project time allocation

**Leave Management**
- Leave applications
- Leave approvals
- Leave balances
- Leave types configuration

### 👥 HR Management

**Employee Management**
- Employee onboarding
- Employee profiles
- Employment history
- Performance tracking

**Department Management**
- Department structure
- Team assignments
- Department reporting
- Hierarchy management

**Document Management**
- Employee documents
- Document templates
- Digital signatures
- Document versioning

### 💰 Financial Management

**Payroll Processing**
- Salary calculations
- Payslip generation
- Tax calculations
- Payment processing

**Expense Management**
- Expense reporting
- Approval workflows
- Expense categories
- Reimbursements

### 📈 System Features

**Dashboard & Analytics**
- Role-based dashboards
- Key performance indicators
- Real-time metrics
- Custom widgets

**Notifications**
- Real-time notifications
- Email notifications
- Push notifications
- Notification preferences

**Search & Reporting**
- Global search functionality
- Advanced filtering
- Custom reports
- Data export capabilities

## 🏛️ API Architecture

### RESTful Design Principles

The API follows REST conventions:

- **Resources**: Identified by URLs
- **HTTP Methods**: GET, POST, PATCH, DELETE
- **Status Codes**: Standard HTTP status codes
- **Stateless**: Each request contains all necessary information

### Resource Naming Conventions

| Resource | Endpoint Pattern | Example |
|----------|------------------|---------|
| Collection | `/resources` | `/employees` |
| Individual | `/resources/{id}` | `/employees/123` |
| Sub-resource | `/resources/{id}/sub` | `/employees/123/documents` |
| Actions | `/resources/{id}/action` | `/attendance/check-in` |

### HTTP Methods Usage

| Method | Purpose | Example |
|--------|---------|---------|
| GET | Retrieve data | `GET /employees` |
| POST | Create new resource | `POST /employees` |
| PATCH | Partial update | `PATCH /employees/123` |
| DELETE | Remove resource | `DELETE /employees/123` |

## 📋 Request/Response Format

### Standard Request Headers

```http
Content-Type: application/json
Authorization: Bearer <jwt-token>
Accept: application/json
```

### Response Format

All API responses follow a consistent structure:

```json
{
  "data": "...",
  "total": 100,
  "page": 1,
  "limit": 10,
  "message": "Success",
  "timestamp": "2025-09-03T10:00:00Z"
}
```

#### Single Resource Response

```json
{
  "data": {
    "id": "uuid",
    "name": "Resource Name",
    "createdAt": "2025-09-03T10:00:00Z",
    "updatedAt": "2025-09-03T10:00:00Z"
  },
  "message": "Success",
  "timestamp": "2025-09-03T10:00:00Z"
}
```

#### Collection Response

```json
{
  "data": [
    {
      "id": "uuid1",
      "name": "Resource 1"
    },
    {
      "id": "uuid2", 
      "name": "Resource 2"
    }
  ],
  "total": 25,
  "page": 1,
  "limit": 10,
  "message": "Success",
  "timestamp": "2025-09-03T10:00:00Z"
}
```

### Error Response Format

```json
{
  "error": {
    "code": "ERROR_CODE",
    "message": "Human readable error message",
    "details": "Additional error details",
    "statusCode": 400
  },
  "timestamp": "2025-09-03T10:00:00Z"
}
```

## 🔍 Pagination

Most collection endpoints support pagination:

### Query Parameters

- `page`: Page number (default: 1)
- `limit`: Items per page (default: 10, max: 100)
- `sort`: Sort field (default: createdAt)
- `order`: Sort order (ASC/DESC, default: DESC)

### Example Request

```bash
GET /employees?page=2&limit=20&sort=fullName&order=ASC
```

### Pagination Response

```json
{
  "data": [...],
  "total": 150,
  "page": 2,
  "limit": 20,
  "totalPages": 8,
  "hasNext": true,
  "hasPrevious": true
}
```

## 🔎 Filtering & Search

### Query Parameters

Most endpoints support filtering through query parameters:

```bash
GET /employees?department=engineering&status=active&search=john
```

### Advanced Search

Global search across multiple resources:

```bash
GET /search/global?q=john+doe&type=employee&limit=20
```

## 📅 Date Handling

### Date Formats

- **Input**: ISO 8601 format (`YYYY-MM-DD` or `YYYY-MM-DDTHH:mm:ssZ`)
- **Output**: ISO 8601 with timezone (`2025-09-03T10:00:00.000Z`)

### Date Range Queries

```bash
GET /attendance?startDate=2025-09-01&endDate=2025-09-30
```

## 🏷️ Field Selection

Optimize responses by specifying required fields:

```bash
GET /employees?fields=id,fullName,email,department.name
```

## 📊 Status Codes

### Success Codes

| Code | Meaning | Usage |
|------|---------|-------|
| 200 | OK | Successful GET, PATCH |
| 201 | Created | Successful POST |
| 204 | No Content | Successful DELETE |

### Client Error Codes

| Code | Meaning | Usage |
|------|---------|-------|
| 400 | Bad Request | Invalid request data |
| 401 | Unauthorized | Missing/invalid token |
| 403 | Forbidden | Insufficient permissions |
| 404 | Not Found | Resource doesn't exist |
| 422 | Validation Error | Invalid input data |
| 429 | Too Many Requests | Rate limit exceeded |

### Server Error Codes

| Code | Meaning | Usage |
|------|---------|-------|
| 500 | Internal Server Error | Server-side error |
| 503 | Service Unavailable | Maintenance mode |

## 🔄 API Versioning

The API uses URL versioning:

- **Current Version**: v1 (implied in base URL)
- **Future Versions**: `/v2/endpoint` when released
- **Backward Compatibility**: v1 maintained for 1 year after v2 release

## 🚀 Rate Limiting

### Limits

- **Authenticated Users**: 1000 requests/hour
- **Admin Users**: 5000 requests/hour
- **Public Endpoints**: 100 requests/hour

### Headers

Rate limit information is included in response headers:

```http
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 999
X-RateLimit-Reset: 1625097600
```

## 🛡️ Security Features

### Data Protection

- **Encryption**: All data encrypted at rest and in transit
- **Input Validation**: Comprehensive input sanitization
- **SQL Injection**: Protection through ORM and parameterized queries
- **XSS Protection**: Output encoding and CSP headers

### Authentication Security

- **JWT Tokens**: Secure token-based authentication
- **Token Expiry**: Configurable token lifetimes
- **Refresh Tokens**: Secure token renewal process
- **Rate Limiting**: Protection against brute force attacks

### Authorization

- **Role-Based Access**: Granular permission system
- **Resource-Level Security**: Entity-level access controls
- **Multi-Tenancy**: Company-isolated data access

## 📚 Common Patterns

### CRUD Operations

```javascript
// Create
POST /employees
{
  "employeeId": "EMP001",
  "user": { ... },
  "department": { ... }
}

// Read
GET /employees/uuid

// Update
PATCH /employees/uuid
{
  "jobTitle": "Senior Developer"
}

// Delete
DELETE /employees/uuid
```

### Bulk Operations

```javascript
// Bulk create
POST /employees/bulk
{
  "employees": [
    { "employeeId": "EMP001", ... },
    { "employeeId": "EMP002", ... }
  ]
}

// Bulk update
PATCH /employees/bulk
{
  "updates": [
    { "id": "uuid1", "jobTitle": "Senior Dev" },
    { "id": "uuid2", "status": "inactive" }
  ]
}
```

### File Uploads

```javascript
POST /documents/upload
Content-Type: multipart/form-data

{
  "file": <binary-data>,
  "title": "Employee Contract",
  "category": "legal"
}
```

## 🧪 Testing the API

### Interactive Documentation

Visit our Swagger UI for interactive API testing:
`https://api.upsurgemedia.in/api`

### Sample Data

Test accounts are available for development:

| Role | Email | Password |
|------|-------|----------|
| Admin | admin@upsurgemedia.com | password123 |
| HR | hr.manager@upsurgemedia.com | password123 |
| Employee | alice.wilson@upsurgemedia.com | password123 |

### Postman Collection

Download our Postman collection for comprehensive API testing:
`https://api.upsurgemedia.in/docs/postman-collection.json`

## 📞 Support & Resources

### Documentation

- **API Reference**: Detailed endpoint documentation
- **Tutorials**: Step-by-step integration guides
- **Examples**: Real-world usage examples
- **SDKs**: Official client libraries

### Support Channels

- **Email**: api-support@upsurgemedia.com
- **Documentation**: This comprehensive guide
- **Status Page**: https://status.upsurgemedia.in
- **Community**: Developer forum and discussions

---

This API overview provides the foundation for understanding and integrating with the Upsurge HRMS API. For detailed endpoint documentation, refer to the specific API sections in this documentation.
