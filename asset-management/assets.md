# 💼 Assets API

The Assets API manages company assets including hardware, software, and other resources assigned to employees.

## Base URL
```
https://api.upsurgemedia.in/api/v1/assets
```

## Endpoints Overview

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/assets` | Create new asset |
| GET | `/assets` | List all assets |
| GET | `/assets/stats` | Get asset statistics |
| GET | `/assets/:id` | Get asset details |
| PATCH | `/assets/:id` | Update asset |
| DELETE | `/assets/:id` | Delete asset |
| POST | `/assets/:id/assign` | Assign asset to employee |
| POST | `/assets/:id/unassign` | Unassign asset |

## Asset Types
- **Hardware**: Laptops, desktops, phones, etc.
- **Software**: License keys, subscriptions
- **Equipment**: Office furniture, tools
- **Vehicle**: Company cars, bikes

## Asset Status
- **Available**: Ready for assignment
- **Assigned**: Currently assigned to employee
- **In Use**: Being used by assigned employee
- **Maintenance**: Under repair or maintenance
- **Retired**: No longer in use

## Authentication
All endpoints require JWT authentication:
```
Authorization: Bearer <your-jwt-token>
```

## Examples

### Create Asset
```bash
curl -X POST "https://api.upsurgemedia.in/api/v1/assets" \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "MacBook Pro 13",
    "type": "hardware",
    "serialNumber": "MP001",
    "model": "MacBook Pro",
    "manufacturer": "Apple",
    "purchaseDate": "2024-01-15",
    "purchasePrice": 1299.99
  }'
```

### Assign Asset
```bash
curl -X POST "https://api.upsurgemedia.in/api/v1/assets/123/assign" \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{
    "employeeId": "emp-456",
    "assignedDate": "2024-01-20",
    "notes": "Assigned for remote work setup"
  }'
```
