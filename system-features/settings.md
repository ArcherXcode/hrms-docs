# ⚙️ Settings API

The Settings API manages system configuration, user preferences, and application settings.

## Base URL
```
https://api.upsurgemedia.in/api/v1/settings
```

## Endpoints Overview

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/settings` | Create setting |
| GET | `/settings` | List all settings |
| GET | `/settings/category/:category` | Get settings by category |
| GET | `/settings/system` | Get system settings |
| PATCH | `/settings/system` | Update system settings |
| GET | `/settings/attendance` | Get attendance settings |
| PATCH | `/settings/attendance` | Update attendance settings |
| GET | `/settings/leave` | Get leave settings |
| PATCH | `/settings/leave` | Update leave settings |
| GET | `/settings/notifications` | Get notification settings |
| PATCH | `/settings/notifications` | Update notification settings |
| GET | `/settings/user/preferences` | Get user preferences |
| POST | `/settings/user/preferences` | Create user preference |
| DELETE | `/settings/user/preferences/:key` | Delete user preference |
| GET | `/settings/:id` | Get specific setting |
| PATCH | `/settings/:id` | Update setting |
| DELETE | `/settings/:id` | Delete setting |

## Setting Categories

### System Settings
- **General**: Application name, timezone, language
- **Security**: Password policies, session timeout
- **Integration**: Third-party service configurations
- **Backup**: Automated backup settings

### Attendance Settings
- **Working Hours**: Standard work schedule
- **Overtime**: Overtime policies and rates
- **Location**: Geofencing and location tracking
- **Break Times**: Allowed break durations

### Leave Settings
- **Leave Types**: Annual, sick, personal leave
- **Accrual**: Leave accumulation rules
- **Approval**: Approval workflow configuration
- **Holidays**: Company holiday calendar

### Notification Settings
- **Email**: Email notification preferences
- **Push**: Mobile push notifications
- **SMS**: Text message notifications
- **In-App**: Application notifications

## Setting Types
- **Boolean**: True/false values
- **String**: Text values
- **Number**: Numeric values
- **JSON**: Complex object values
- **Array**: List values

## Authentication
All endpoints require JWT authentication:
```
Authorization: Bearer <your-jwt-token>
```

## Examples

### Get System Settings
```bash
curl -X GET "https://api.upsurgemedia.in/api/v1/settings/system" \
  -H "Authorization: Bearer <token>"
```

### Update Attendance Settings
```bash
curl -X PATCH "https://api.upsurgemedia.in/api/v1/settings/attendance" \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{
    "workingHours": {
      "start": "09:00",
      "end": "17:00"
    },
    "allowOvertimeAutoApproval": true,
    "maxOvertimeHoursPerDay": 4
  }'
```

### Create User Preference
```bash
curl -X POST "https://api.upsurgemedia.in/api/v1/settings/user/preferences" \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{
    "key": "theme",
    "value": "dark",
    "category": "appearance"
  }'
```
