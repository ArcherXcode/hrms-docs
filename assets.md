# Assets API

The Assets API manages company assets, their assignment to employees, maintenance tracking, and asset-related statistics.

## Endpoints Overview

| Method | Endpoint | Description | Access |
|--------|----------|-------------|---------|
| GET | `/assets` | List all assets | All authenticated |
| GET | `/assets/:id` | Get asset by ID | All authenticated |
| POST | `/assets` | Create new asset | HR, Admin |
| PUT | `/assets/:id` | Update asset | HR, Admin |
| DELETE | `/assets/:id` | Delete asset | Admin |
| POST | `/assets/:id/assign` | Assign asset to employee | HR, Admin |
| POST | `/assets/:id/unassign` | Unassign asset | HR, Admin |
| GET | `/assets/stats/overview` | Get asset statistics | HR, Admin |

## GET /assets

Retrieve assets with filtering and pagination.

### Request

```http
GET /api/assets?page=1&limit=10&category=laptop&status=assigned&assignedTo=user-001
Authorization: Bearer <token>
```

### Query Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| page | number | 1 | Page number for pagination |
| limit | number | 10 | Number of records per page |
| category | string | - | Filter by category |
| status | string | - | Filter by status (available, assigned, maintenance, retired) |
| assignedTo | string | - | Filter by assigned user ID |
| search | string | - | Search in name, serial number, model |

### Response

```json
{
  "success": true,
  "data": {
    "assets": [
      {
        "id": "asset-001",
        "name": "MacBook Pro 16\"",
        "category": "laptop",
        "serialNumber": "C02X1234ABCD",
        "model": "MacBook Pro",
        "brand": "Apple",
        "purchaseDate": "2024-01-15",
        "purchasePrice": 2500.00,
        "currentValue": 2000.00,
        "status": "assigned",
        "condition": "excellent",
        "warranty": {
          "startDate": "2024-01-15",
          "endDate": "2027-01-15",
          "provider": "Apple Inc."
        },
        "assignedTo": {
          "id": "user-001",
          "firstName": "John",
          "lastName": "Doe",
          "employeeId": "EMP001"
        },
        "assignedAt": "2024-01-20T10:00:00Z",
        "location": "Office - Floor 3",
        "createdAt": "2024-01-15T14:30:00Z",
        "updatedAt": "2024-01-20T10:00:00Z"
      }
    ],
    "pagination": {
      "page": 1,
      "limit": 10,
      "total": 125,
      "pages": 13
    }
  }
}
```

## GET /assets/:id

Get detailed information about a specific asset.

### Request

```http
GET /api/assets/asset-001
Authorization: Bearer <token>
```

### Response

```json
{
  "success": true,
  "data": {
    "asset": {
      "id": "asset-001",
      "name": "MacBook Pro 16\"",
      "category": "laptop",
      "serialNumber": "C02X1234ABCD",
      "model": "MacBook Pro",
      "brand": "Apple",
      "description": "16-inch MacBook Pro with M2 Pro chip",
      "purchaseDate": "2024-01-15",
      "purchasePrice": 2500.00,
      "currentValue": 2000.00,
      "depreciationRate": 20,
      "status": "assigned",
      "condition": "excellent",
      "warranty": {
        "startDate": "2024-01-15",
        "endDate": "2027-01-15",
        "provider": "Apple Inc.",
        "coverage": "Full hardware and software support"
      },
      "specifications": {
        "processor": "Apple M2 Pro",
        "memory": "16GB",
        "storage": "512GB SSD",
        "display": "16-inch Liquid Retina XDR"
      },
      "assignedTo": {
        "id": "user-001",
        "firstName": "John",
        "lastName": "Doe",
        "employeeId": "EMP001",
        "department": "Engineering",
        "email": "john.doe@company.com"
      },
      "assignedAt": "2024-01-20T10:00:00Z",
      "assignedBy": {
        "id": "hr-001",
        "firstName": "Jane",
        "lastName": "Smith"
      },
      "location": "Office - Floor 3, Desk 42",
      "maintenanceHistory": [
        {
          "date": "2024-02-15",
          "type": "routine",
          "description": "Software update and cleaning",
          "cost": 0,
          "performedBy": "IT Support"
        }
      ],
      "notes": "High-performance laptop for development work",
      "tags": ["development", "mobile", "high-priority"],
      "createdAt": "2024-01-15T14:30:00Z",
      "updatedAt": "2024-02-15T09:00:00Z"
    }
  }
}
```

## POST /assets

Create a new asset record.

### Request

```http
POST /api/assets
Authorization: Bearer <token>
Content-Type: application/json

{
  "name": "Dell Monitor 27\"",
  "category": "monitor",
  "serialNumber": "DM271234567",
  "model": "UltraSharp U2722DE",
  "brand": "Dell",
  "description": "27-inch 4K USB-C monitor",
  "purchaseDate": "2024-02-01",
  "purchasePrice": 450.00,
  "warranty": {
    "startDate": "2024-02-01",
    "endDate": "2027-02-01",
    "provider": "Dell Technologies"
  },
  "specifications": {
    "resolution": "3840x2160",
    "connectivity": "USB-C, HDMI, DisplayPort",
    "features": "Height adjustable, USB hub"
  },
  "location": "Warehouse - Section A",
  "condition": "new"
}
```

### Request Body Schema

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| name | string | Yes | Asset name |
| category | string | Yes | Asset category |
| serialNumber | string | Yes | Unique serial number |
| model | string | No | Asset model |
| brand | string | No | Asset brand |
| description | string | No | Asset description |
| purchaseDate | date | No | Purchase date |
| purchasePrice | number | No | Purchase price |
| warranty | object | No | Warranty details |
| specifications | object | No | Technical specifications |
| location | string | No | Physical location |
| condition | string | No | Asset condition |

### Response

```json
{
  "success": true,
  "message": "Asset created successfully",
  "data": {
    "asset": {
      "id": "asset-002",
      "name": "Dell Monitor 27\"",
      "category": "monitor",
      "serialNumber": "DM271234567",
      "status": "available",
      "condition": "new",
      "currentValue": 450.00,
      "createdAt": "2024-02-01T11:00:00Z"
    }
  }
}
```

## PUT /assets/:id

Update an existing asset.

### Request

```http
PUT /api/assets/asset-001
Authorization: Bearer <token>
Content-Type: application/json

{
  "condition": "good",
  "currentValue": 1800.00,
  "location": "Office - Floor 2, Desk 25",
  "notes": "Minor scratch on lid, otherwise excellent"
}
```

### Response

```json
{
  "success": true,
  "message": "Asset updated successfully",
  "data": {
    "asset": {
      "id": "asset-001",
      "condition": "good",
      "currentValue": 1800.00,
      "location": "Office - Floor 2, Desk 25",
      "notes": "Minor scratch on lid, otherwise excellent",
      "updatedAt": "2024-02-20T14:15:00Z"
    }
  }
}
```

## POST /assets/:id/assign

Assign an asset to an employee.

### Request

```http
POST /api/assets/asset-002/assign
Authorization: Bearer <token>
Content-Type: application/json

{
  "userId": "user-002",
  "assignmentDate": "2024-02-02",
  "notes": "Monitor assigned for remote work setup"
}
```

### Request Body Schema

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| userId | string | Yes | Employee UUID to assign to |
| assignmentDate | date | No | Assignment date (default: today) |
| notes | string | No | Assignment notes |

### Response

```json
{
  "success": true,
  "message": "Asset assigned successfully",
  "data": {
    "assignment": {
      "assetId": "asset-002",
      "userId": "user-002",
      "assignedAt": "2024-02-02T09:00:00Z",
      "assignedBy": "hr-001",
      "notes": "Monitor assigned for remote work setup",
      "asset": {
        "name": "Dell Monitor 27\"",
        "status": "assigned"
      },
      "user": {
        "firstName": "Alice",
        "lastName": "Johnson",
        "employeeId": "EMP002"
      }
    }
  }
}
```

## POST /assets/:id/unassign

Unassign an asset from its current user.

### Request

```http
POST /api/assets/asset-002/unassign
Authorization: Bearer <token>
Content-Type: application/json

{
  "reason": "Employee resignation",
  "condition": "good",
  "notes": "Asset returned in good condition"
}
```

### Request Body Schema

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| reason | string | No | Reason for unassignment |
| condition | string | No | Asset condition upon return |
| notes | string | No | Return notes |

### Response

```json
{
  "success": true,
  "message": "Asset unassigned successfully",
  "data": {
    "asset": {
      "id": "asset-002",
      "status": "available",
      "assignedTo": null,
      "assignedAt": null,
      "returnedAt": "2024-02-20T16:00:00Z",
      "condition": "good"
    }
  }
}
```

## DELETE /assets/:id

Delete an asset record (Admin only).

### Request

```http
DELETE /api/assets/asset-001
Authorization: Bearer <token>
```

### Response

```json
{
  "success": true,
  "message": "Asset deleted successfully"
}
```

## GET /assets/stats/overview

Get comprehensive asset statistics.

### Request

```http
GET /api/assets/stats/overview
Authorization: Bearer <token>
```

### Response

```json
{
  "success": true,
  "data": {
    "stats": {
      "totalAssets": 125,
      "totalValue": 125000.00,
      "byStatus": {
        "available": 25,
        "assigned": 85,
        "maintenance": 10,
        "retired": 5
      },
      "byCategory": {
        "laptop": 45,
        "desktop": 20,
        "monitor": 30,
        "phone": 25,
        "tablet": 5
      },
      "byCondition": {
        "new": 15,
        "excellent": 40,
        "good": 55,
        "fair": 12,
        "poor": 3
      },
      "utilizationRate": 68.0,
      "averageAge": 18,
      "upcomingMaintenance": 8,
      "expiringWarranties": 5,
      "topCategories": [
        {
          "category": "laptop",
          "count": 45,
          "value": 65000.00
        },
        {
          "category": "monitor",
          "count": 30,
          "value": 25000.00
        }
      ],
      "recentAssignments": [
        {
          "assetId": "asset-002",
          "assetName": "Dell Monitor 27\"",
          "userName": "Alice Johnson",
          "assignedAt": "2024-02-02T09:00:00Z"
        }
      ]
    }
  }
}
```

## Asset Categories

| Category | Description | Examples |
|----------|-------------|----------|
| laptop | Portable computers | MacBook, ThinkPad, Surface |
| desktop | Desktop computers | iMac, Dell OptiPlex |
| monitor | Display screens | LED, LCD, OLED monitors |
| phone | Mobile devices | iPhone, Android phones |
| tablet | Tablet devices | iPad, Surface Pro |
| printer | Printing devices | Laser, Inkjet printers |
| furniture | Office furniture | Desks, chairs, cabinets |
| software | Software licenses | Office, Adobe, development tools |

## Asset Status Flow

```
available → assigned → maintenance → available
available → assigned → retired
maintenance → available
maintenance → retired
```

## Error Responses

### Asset Already Assigned (400)

```json
{
  "success": false,
  "message": "Asset is already assigned to another user"
}
```

### Asset Not Found (404)

```json
{
  "success": false,
  "message": "Asset not found"
}
```

### Serial Number Exists (400)

```json
{
  "success": false,
  "message": "Asset with this serial number already exists"
}
```

## Usage Examples

### JavaScript

```javascript
// Fetch assets with filters
const getAssets = async (filters = {}) => {
  const params = new URLSearchParams({
    page: 1,
    limit: 20,
    ...filters
  });
  
  const response = await fetch(`/api/assets?${params}`, {
    headers: {
      'Authorization': `Bearer ${token}`
    }
  });
  return response.json();
};

// Assign asset to user
const assignAsset = async (assetId, userId, notes) => {
  const response = await fetch(`/api/assets/${assetId}/assign`, {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${token}`,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      userId,
      assignmentDate: new Date().toISOString().split('T')[0],
      notes
    })
  });
  return response.json();
};

// Create new asset
const createAsset = async (assetData) => {
  const response = await fetch('/api/assets', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${token}`,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify(assetData)
  });
  return response.json();
};

// Get asset statistics
const getAssetStats = async () => {
  const response = await fetch('/api/assets/stats/overview', {
    headers: {
      'Authorization': `Bearer ${token}`
    }
  });
  return response.json();
};
```

### Asset Management Workflow

```javascript
// Complete asset assignment workflow
const processAssetAssignment = async (assetId, employeeId) => {
  try {
    // Step 1: Check asset availability
    const assetResponse = await fetch(`/api/assets/${assetId}`, {
      headers: { 'Authorization': `Bearer ${token}` }
    });
    const assetData = await assetResponse.json();
    
    if (assetData.data.asset.status !== 'available') {
      throw new Error('Asset is not available for assignment');
    }
    
    // Step 2: Assign the asset
    const assignResponse = await fetch(`/api/assets/${assetId}/assign`, {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${token}`,
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({
        userId: employeeId,
        notes: 'Assigned via automated workflow'
      })
    });
    
    const result = await assignResponse.json();
    
    if (result.success) {
      console.log(`Asset ${assetId} assigned to employee ${employeeId}`);
      return result;
    } else {
      throw new Error(result.message);
    }
  } catch (error) {
    console.error('Asset assignment failed:', error);
    throw error;
  }
};
```
