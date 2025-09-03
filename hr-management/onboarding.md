# 👥 Onboarding API

The Onboarding API manages new employee onboarding processes, tasks, and progress tracking.

## Base URL
```
https://api.upsurgemedia.in/api/v1/onboarding
```

## Endpoints Overview

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/onboarding/tasks` | Create onboarding task |
| GET | `/onboarding/tasks` | List onboarding tasks |
| GET | `/onboarding/tasks/:id` | Get task details |
| PATCH | `/onboarding/tasks/:id` | Update task |
| DELETE | `/onboarding/tasks/:id` | Delete task |
| POST | `/onboarding/progress` | Create progress entry |
| GET | `/onboarding/progress/employee/:employeeId` | Get employee progress |
| PATCH | `/onboarding/progress/:id` | Update progress |
| GET | `/onboarding/status/:employeeId` | Get onboarding status |
| POST | `/onboarding/initialize/:employeeId` | Initialize employee onboarding |
| GET | `/onboarding/statistics/company` | Get company onboarding stats |

## Onboarding Task Types
- **Documentation**: Forms, contracts, policies
- **Training**: Mandatory training sessions
- **Equipment**: Asset assignment and setup
- **Access**: System access and credentials
- **Introduction**: Team meetings and introductions

## Task Status
- **Pending**: Not yet started
- **In Progress**: Currently being worked on
- **Completed**: Successfully finished
- **Skipped**: Skipped or not applicable
- **Overdue**: Past due date

## Authentication
All endpoints require JWT authentication:
```
Authorization: Bearer <your-jwt-token>
```

## Examples

### Initialize Employee Onboarding
```bash
curl -X POST "https://api.upsurgemedia.in/api/v1/onboarding/initialize/emp-123" \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json"
```

### Create Onboarding Task
```bash
curl -X POST "https://api.upsurgemedia.in/api/v1/onboarding/tasks" \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Complete I-9 Form",
    "description": "Fill out and submit employment eligibility verification",
    "type": "documentation",
    "category": "Legal",
    "dueDate": "2024-01-25",
    "isRequired": true,
    "assignedRole": "employee"
  }'
```

### Get Employee Onboarding Status
```bash
curl -X GET "https://api.upsurgemedia.in/api/v1/onboarding/status/emp-123" \
  -H "Authorization: Bearer <token>"
```
