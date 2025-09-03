# 🔍 Search API

The Search API provides global search functionality across all system entities and data.

## Base URL
```
https://api.upsurgemedia.in/api/v1/search
```

## Endpoints Overview

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/search/global` | Global search across all entities |
| GET | `/search/employees` | Search employees specifically |
| GET | `/search/suggestions` | Get search suggestions |

## Search Features

### Global Search
Search across multiple entity types:
- **Employees**: Names, positions, departments
- **Companies**: Company names, addresses
- **Departments**: Department names, descriptions
- **Documents**: Document titles, content
- **Assets**: Asset names, serial numbers
- **Expenses**: Expense descriptions, vendors

### Employee-Specific Search
Advanced employee search with filters:
- Department
- Position/Role
- Employment status
- Location
- Skills/Qualifications

### Search Suggestions
Intelligent suggestions based on:
- Previous searches
- Popular queries
- Related entities
- Auto-complete functionality

## Query Parameters

### Global Search
- `q` (string): Search query
- `type` (string): Entity type filter
- `limit` (number): Results limit (default: 20)
- `offset` (number): Pagination offset

### Employee Search
- `q` (string): Search query
- `department` (string): Department filter
- `status` (string): Employment status
- `position` (string): Job position
- `location` (string): Location filter

## Authentication
All endpoints require JWT authentication:
```
Authorization: Bearer <your-jwt-token>
```

## Examples

### Global Search
```bash
curl -X GET "https://api.upsurgemedia.in/api/v1/search/global?q=john&limit=10" \
  -H "Authorization: Bearer <token>"
```

### Search Employees
```bash
curl -X GET "https://api.upsurgemedia.in/api/v1/search/employees?q=developer&department=engineering" \
  -H "Authorization: Bearer <token>"
```

### Get Search Suggestions
```bash
curl -X GET "https://api.upsurgemedia.in/api/v1/search/suggestions?q=jo" \
  -H "Authorization: Bearer <token>"
```

## Response Format

### Global Search Response
```json
{
  "success": true,
  "data": {
    "results": [
      {
        "type": "employee",
        "id": "emp-123",
        "title": "John Doe",
        "subtitle": "Senior Developer - Engineering",
        "description": "Full-stack developer with 5 years experience",
        "url": "/employees/emp-123"
      }
    ],
    "total": 15,
    "categories": {
      "employees": 8,
      "documents": 4,
      "assets": 3
    }
  }
}
```
