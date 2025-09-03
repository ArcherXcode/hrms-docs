# 🏢 Organization Structure API

The Organization API manages company hierarchy, reporting structures, and organizational charts.

## Base URL
```
https://api.upsurgemedia.in/api/v1/organization
```

## Endpoints Overview

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/organization/structure` | Get organization structure |
| POST | `/organization/structure` | Create organization unit |
| GET | `/organization/hierarchy` | Get company hierarchy |
| GET | `/organization/job-roles` | Get all job roles |
| POST | `/organization/job-roles` | Create job role |
| GET | `/organization/reporting-structure/:employeeId` | Get employee reporting structure |
| GET | `/organization/team-members/:managerId` | Get team members |
| GET | `/organization/departments/tree` | Get department tree view |

## Features

### Organizational Hierarchy
- Multi-level reporting structures
- Manager-employee relationships
- Department and sub-department organization
- Role-based permissions

### Job Roles Management
- Position definitions
- Responsibility assignments
- Salary grade mappings
- Career progression paths

## Authentication
All endpoints require JWT authentication:
```
Authorization: Bearer <your-jwt-token>
```

## Examples

### Get Organization Structure
```bash
curl -X GET "https://api.upsurgemedia.in/api/v1/organization/structure" \
  -H "Authorization: Bearer <token>"
```

### Get Team Members
```bash
curl -X GET "https://api.upsurgemedia.in/api/v1/organization/team-members/manager-123" \
  -H "Authorization: Bearer <token>"
```

### Create Job Role
```bash
curl -X POST "https://api.upsurgemedia.in/api/v1/organization/job-roles" \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Senior Software Developer",
    "department": "Engineering",
    "level": "Senior",
    "description": "Lead development of software solutions",
    "requirements": ["5+ years experience", "React/Node.js expertise"]
  }'
```
