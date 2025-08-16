# Documents API

The Documents API manages file storage, sharing, access control, and document-related operations within the HRMS system.

## Endpoints Overview

| Method | Endpoint | Description | Access |
|--------|----------|-------------|---------|
| GET | `/documents` | List documents | All authenticated |
| GET | `/documents/:id` | Get document details | Owner, Shared users, HR, Admin |
| POST | `/documents` | Upload document | All authenticated |
| PUT | `/documents/:id` | Update document | Owner, HR, Admin |
| DELETE | `/documents/:id` | Delete document | Owner, Admin |
| POST | `/documents/:id/share` | Share document | Owner, HR, Admin |
| DELETE | `/documents/:id/share/:userId` | Remove document access | Owner, HR, Admin |
| GET | `/documents/:id/download` | Download document | Authorized users |
| GET | `/documents/stats/overview` | Get document statistics | HR, Admin |

## GET /documents

Retrieve documents with filtering and pagination.

### Request

```http
GET /api/documents?page=1&limit=10&type=pdf&shared=true&category=policy
Authorization: Bearer <token>
```

### Query Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| page | number | 1 | Page number for pagination |
| limit | number | 10 | Number of records per page |
| type | string | - | Filter by file type (pdf, doc, image, etc.) |
| category | string | - | Filter by category |
| shared | boolean | - | Filter shared documents |
| search | string | - | Search in filename and description |
| uploadedBy | string | - | Filter by uploader (HR/Admin only) |

### Response

```json
{
  "success": true,
  "data": {
    "documents": [
      {
        "id": "doc-001",
        "filename": "Employee_Handbook_2024.pdf",
        "originalName": "Employee Handbook 2024.pdf",
        "type": "pdf",
        "size": 2048000,
        "category": "policy",
        "description": "Company employee handbook for 2024",
        "uploadedBy": {
          "id": "hr-001",
          "firstName": "Jane",
          "lastName": "Smith",
          "role": "hr"
        },
        "uploadedAt": "2024-01-15T10:00:00Z",
        "isShared": true,
        "sharedWith": ["user-001", "user-002"],
        "accessCount": 25,
        "lastAccessedAt": "2024-02-20T14:30:00Z",
        "tags": ["handbook", "policy", "2024"],
        "version": 1,
        "status": "active"
      }
    ],
    "pagination": {
      "page": 1,
      "limit": 10,
      "total": 85,
      "pages": 9
    }
  }
}
```

## GET /documents/:id

Get detailed information about a specific document.

### Request

```http
GET /api/documents/doc-001
Authorization: Bearer <token>
```

### Response

```json
{
  "success": true,
  "data": {
    "document": {
      "id": "doc-001",
      "filename": "Employee_Handbook_2024.pdf",
      "originalName": "Employee Handbook 2024.pdf",
      "type": "pdf",
      "size": 2048000,
      "category": "policy",
      "description": "Company employee handbook for 2024",
      "uploadedBy": {
        "id": "hr-001",
        "firstName": "Jane",
        "lastName": "Smith",
        "role": "hr",
        "department": "Human Resources"
      },
      "uploadedAt": "2024-01-15T10:00:00Z",
      "updatedAt": "2024-01-20T09:00:00Z",
      "isShared": true,
      "accessLevel": "read",
      "sharedWith": [
        {
          "userId": "user-001",
          "userName": "John Doe",
          "sharedAt": "2024-01-15T11:00:00Z",
          "accessLevel": "read"
        },
        {
          "userId": "user-002",
          "userName": "Alice Johnson",
          "sharedAt": "2024-01-15T11:00:00Z",
          "accessLevel": "read"
        }
      ],
      "accessLog": [
        {
          "userId": "user-001",
          "userName": "John Doe",
          "action": "download",
          "timestamp": "2024-02-20T14:30:00Z",
          "ipAddress": "192.168.1.100"
        }
      ],
      "tags": ["handbook", "policy", "2024"],
      "version": 1,
      "status": "active",
      "metadata": {
        "contentType": "application/pdf",
        "checksum": "a1b2c3d4e5f6",
        "pages": 45
      },
      "downloadUrl": "/api/documents/doc-001/download",
      "permissions": {
        "canEdit": true,
        "canDelete": true,
        "canShare": true
      }
    }
  }
}
```

## POST /documents

Upload a new document.

### Request

```http
POST /api/documents
Authorization: Bearer <token>
Content-Type: multipart/form-data

{
  "file": [binary file data],
  "category": "policy",
  "description": "Updated company policies",
  "tags": "policy,2024,updated",
  "isShared": true,
  "shareWith": "user-001,user-002"
}
```

### Request Body Schema (multipart/form-data)

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| file | file | Yes | Document file to upload |
| category | string | No | Document category |
| description | string | No | Document description |
| tags | string | No | Comma-separated tags |
| isShared | boolean | No | Whether to share document |
| shareWith | string | No | Comma-separated user IDs to share with |

### Response

```json
{
  "success": true,
  "message": "Document uploaded successfully",
  "data": {
    "document": {
      "id": "doc-002",
      "filename": "Company_Policies_2024.pdf",
      "originalName": "Company Policies 2024.pdf",
      "type": "pdf",
      "size": 1024000,
      "category": "policy",
      "description": "Updated company policies",
      "uploadedBy": "hr-001",
      "uploadedAt": "2024-02-01T15:00:00Z",
      "tags": ["policy", "2024", "updated"],
      "status": "active"
    }
  }
}
```

## PUT /documents/:id

Update document information (not the file itself).

### Request

```http
PUT /api/documents/doc-001
Authorization: Bearer <token>
Content-Type: application/json

{
  "description": "Updated employee handbook with new policies",
  "category": "policy",
  "tags": ["handbook", "policy", "2024", "updated"]
}
```

### Request Body Schema

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| description | string | No | Updated description |
| category | string | No | Updated category |
| tags | array | No | Updated tags array |

### Response

```json
{
  "success": true,
  "message": "Document updated successfully",
  "data": {
    "document": {
      "id": "doc-001",
      "description": "Updated employee handbook with new policies",
      "category": "policy",
      "tags": ["handbook", "policy", "2024", "updated"],
      "updatedAt": "2024-02-21T10:30:00Z"
    }
  }
}
```

## POST /documents/:id/share

Share a document with users.

### Request

```http
POST /api/documents/doc-001/share
Authorization: Bearer <token>
Content-Type: application/json

{
  "userIds": ["user-003", "user-004"],
  "accessLevel": "read",
  "message": "Please review the updated handbook"
}
```

### Request Body Schema

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| userIds | array | Yes | Array of user IDs to share with |
| accessLevel | string | No | Access level (read, edit) - default: read |
| message | string | No | Optional message to recipients |

### Response

```json
{
  "success": true,
  "message": "Document shared successfully",
  "data": {
    "sharedWith": [
      {
        "userId": "user-003",
        "userName": "Bob Wilson",
        "accessLevel": "read",
        "sharedAt": "2024-02-21T11:00:00Z"
      },
      {
        "userId": "user-004",
        "userName": "Carol Davis",
        "accessLevel": "read",
        "sharedAt": "2024-02-21T11:00:00Z"
      }
    ]
  }
}
```

## DELETE /documents/:id/share/:userId

Remove document access from a specific user.

### Request

```http
DELETE /api/documents/doc-001/share/user-003
Authorization: Bearer <token>
```

### Response

```json
{
  "success": true,
  "message": "Document access removed successfully"
}
```

## GET /documents/:id/download

Download a document file.

### Request

```http
GET /api/documents/doc-001/download
Authorization: Bearer <token>
```

### Response

**Success (200 OK)**
- Returns the file with appropriate headers
- Content-Type: Based on file type
- Content-Disposition: attachment; filename="document.pdf"

**Error (403 Forbidden)**

```json
{
  "success": false,
  "message": "Access denied. Document not shared with you."
}
```

## DELETE /documents/:id

Delete a document (Owner/Admin only).

### Request

```http
DELETE /api/documents/doc-001
Authorization: Bearer <token>
```

### Response

```json
{
  "success": true,
  "message": "Document deleted successfully"
}
```

## GET /documents/stats/overview

Get document statistics and analytics.

### Request

```http
GET /api/documents/stats/overview
Authorization: Bearer <token>
```

### Response

```json
{
  "success": true,
  "data": {
    "stats": {
      "totalDocuments": 85,
      "totalSize": 125000000,
      "sharedDocuments": 32,
      "byType": {
        "pdf": 45,
        "doc": 20,
        "xlsx": 10,
        "jpg": 5,
        "png": 3,
        "txt": 2
      },
      "byCategory": {
        "policy": 25,
        "form": 20,
        "manual": 15,
        "certificate": 12,
        "report": 8,
        "other": 5
      },
      "uploadsThisMonth": 12,
      "downloadsThisMonth": 156,
      "mostAccessed": [
        {
          "documentId": "doc-001",
          "filename": "Employee_Handbook_2024.pdf",
          "accessCount": 45,
          "lastAccessed": "2024-02-20T14:30:00Z"
        }
      ],
      "recentUploads": [
        {
          "documentId": "doc-002",
          "filename": "Company_Policies_2024.pdf",
          "uploadedBy": "Jane Smith",
          "uploadedAt": "2024-02-01T15:00:00Z"
        }
      ],
      "storageUsed": {
        "total": 125000000,
        "limit": 1000000000,
        "percentage": 12.5
      }
    }
  }
}
```

## Document Categories

| Category | Description | Examples |
|----------|-------------|----------|
| policy | Company policies | Employee handbook, code of conduct |
| form | Forms and templates | Leave forms, expense forms |
| manual | User manuals | Software guides, process manuals |
| certificate | Certificates | Training certificates, compliance docs |
| report | Reports | Monthly reports, analytics |
| contract | Contracts | Employment contracts, vendor agreements |
| invoice | Invoices | Purchase invoices, expense receipts |
| other | Miscellaneous | General documents |

## File Type Restrictions

### Allowed File Types
- **Documents**: pdf, doc, docx, txt, rtf
- **Spreadsheets**: xls, xlsx, csv
- **Images**: jpg, jpeg, png, gif
- **Archives**: zip, rar
- **Presentations**: ppt, pptx

### Size Limits
- Maximum file size: 25MB per file
- Total storage per organization: 1GB (configurable)

## Access Levels

| Level | Permissions |
|-------|-------------|
| read | View and download document |
| edit | View, download, and update document info |
| admin | Full access including delete and share |

## Error Responses

### File Too Large (413)

```json
{
  "success": false,
  "message": "File size exceeds maximum limit of 25MB"
}
```

### Invalid File Type (400)

```json
{
  "success": false,
  "message": "File type not allowed. Supported types: pdf, doc, docx, txt, jpg, png"
}
```

### Storage Quota Exceeded (400)

```json
{
  "success": false,
  "message": "Storage quota exceeded. Please delete some files first."
}
```

## Usage Examples

### JavaScript File Upload

```javascript
// Upload document with file
const uploadDocument = async (file, metadata = {}) => {
  const formData = new FormData();
  formData.append('file', file);
  
  // Add metadata
  Object.keys(metadata).forEach(key => {
    formData.append(key, metadata[key]);
  });
  
  const response = await fetch('/api/documents', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${token}`
      // Don't set Content-Type for FormData
    },
    body: formData
  });
  
  return response.json();
};

// Download document
const downloadDocument = async (documentId) => {
  const response = await fetch(`/api/documents/${documentId}/download`, {
    headers: {
      'Authorization': `Bearer ${token}`
    }
  });
  
  if (response.ok) {
    const blob = await response.blob();
    const url = window.URL.createObjectURL(blob);
    const a = document.createElement('a');
    a.href = url;
    a.download = 'document'; // Filename will be in Content-Disposition header
    a.click();
    window.URL.revokeObjectURL(url);
  }
  
  return response.json();
};

// Share document
const shareDocument = async (documentId, userIds, message) => {
  const response = await fetch(`/api/documents/${documentId}/share`, {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${token}`,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      userIds,
      accessLevel: 'read',
      message
    })
  });
  
  return response.json();
};
```

### React File Upload Component

```javascript
import { useState } from 'react';

const DocumentUpload = () => {
  const [file, setFile] = useState(null);
  const [uploading, setUploading] = useState(false);
  const [metadata, setMetadata] = useState({
    category: 'other',
    description: '',
    tags: []
  });

  const handleUpload = async (e) => {
    e.preventDefault();
    if (!file) return;

    setUploading(true);
    try {
      const formData = new FormData();
      formData.append('file', file);
      formData.append('category', metadata.category);
      formData.append('description', metadata.description);
      formData.append('tags', metadata.tags.join(','));

      const response = await fetch('/api/documents', {
        method: 'POST',
        headers: {
          'Authorization': `Bearer ${localStorage.getItem('token')}`
        },
        body: formData
      });

      const result = await response.json();
      
      if (result.success) {
        console.log('Document uploaded successfully');
        setFile(null);
        setMetadata({ category: 'other', description: '', tags: [] });
      } else {
        console.error('Upload failed:', result.message);
      }
    } catch (error) {
      console.error('Upload error:', error);
    } finally {
      setUploading(false);
    }
  };

  return (
    <form onSubmit={handleUpload}>
      <input
        type="file"
        onChange={(e) => setFile(e.target.files[0])}
        accept=".pdf,.doc,.docx,.txt,.jpg,.png"
      />
      
      <select 
        value={metadata.category}
        onChange={(e) => setMetadata({...metadata, category: e.target.value})}
      >
        <option value="policy">Policy</option>
        <option value="form">Form</option>
        <option value="manual">Manual</option>
        <option value="other">Other</option>
      </select>
      
      <textarea
        placeholder="Description"
        value={metadata.description}
        onChange={(e) => setMetadata({...metadata, description: e.target.value})}
      />
      
      <button type="submit" disabled={!file || uploading}>
        {uploading ? 'Uploading...' : 'Upload Document'}
      </button>
    </form>
  );
};
```
