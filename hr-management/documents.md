# 📄 Documents API

The Documents API provides comprehensive document management functionality including employee document storage, templates, digital signatures, and document lifecycle management.

## Base Endpoint

```
https://api.upsurgemedia.in/documents
```

## Overview

The Documents API enables organizations to manage all employee-related documents digitally, from onboarding paperwork to performance reviews, with support for templates, approval workflows, and secure storage.

## Document Models

```typescript
interface Document {
  id: string;                    // UUID
  title: string;                 // Document title
  fileName: string;              // Original file name
  fileSize: number;              // File size in bytes
  mimeType: string;              // MIME type (e.g., application/pdf)
  filePath: string;              // Storage path
  
  // Categorization
  type: DocumentType;            // Document type category
  category: string;              // Custom category
  tags?: string[];               // Document tags
  
  // Ownership and Access
  employeeId?: string;           // Associated employee
  companyId: string;             // Company ID
  departmentId?: string;         // Associated department
  
  // Content and Description
  description?: string;          // Document description
  version: number;               // Document version
  isTemplate: boolean;           // Template document flag
  
  // Status and Workflow
  status: DocumentStatus;        // Draft, active, archived, etc.
  isConfidential: boolean;       // Confidential flag
  requiresSignature: boolean;    // Digital signature required
  expiryDate?: Date;            // Document expiry date
  
  // Approval Workflow
  approvalStatus?: ApprovalStatus; // Pending, approved, rejected
  approvedBy?: string;           // Approver user ID
  approvedAt?: Date;             // Approval timestamp
  rejectionReason?: string;      // Rejection reason
  
  // Audit Trail
  uploadedBy: string;            // Uploader user ID
  uploadedAt: Date;              // Upload timestamp
  lastAccessedAt?: Date;         // Last access timestamp
  downloadCount: number;         // Download count
  
  // Timestamps
  createdAt: Date;
  updatedAt: Date;
  
  // Relationships
  employee?: Employee;           // Associated employee
  uploader?: User;               // Document uploader
  approver?: User;               // Document approver
  signatures?: DocumentSignature[]; // Digital signatures
  versions?: Document[];         // Document versions
}

interface DocumentTemplate {
  id: string;                    // UUID
  name: string;                  // Template name
  description?: string;          // Template description
  type: DocumentType;            // Document type
  companyId: string;             // Company ID
  
  // Template Content
  content: string;               // Template HTML/text content
  fields: TemplateField[];       // Dynamic fields
  
  // Configuration
  isActive: boolean;             // Template status
  requiresApproval: boolean;     // Approval required
  autoGenerate: boolean;         // Auto-generate on trigger
  
  // Usage
  usageCount: number;            // Times used
  
  // Timestamps
  createdAt: Date;
  updatedAt: Date;
}

interface DocumentSignature {
  id: string;                    // UUID
  documentId: string;            // Document ID
  signerUserId: string;          // Signer user ID
  signatureData: string;         // Signature image/data
  signedAt: Date;                // Signature timestamp
  ipAddress: string;             // Signer IP address
  isValid: boolean;              // Signature validity
}

enum DocumentType {
  CONTRACT = 'contract',
  POLICY = 'policy',
  FORM = 'form',
  CERTIFICATE = 'certificate',
  IDENTIFICATION = 'identification',
  RESUME = 'resume',
  REVIEW = 'review',
  TRAINING = 'training',
  COMPLIANCE = 'compliance',
  OTHER = 'other'
}

enum DocumentStatus {
  DRAFT = 'draft',
  ACTIVE = 'active',
  ARCHIVED = 'archived',
  EXPIRED = 'expired',
  DELETED = 'deleted'
}

enum ApprovalStatus {
  PENDING = 'pending',
  APPROVED = 'approved',
  REJECTED = 'rejected',
  REQUIRES_CHANGES = 'requires_changes'
}
```

## Document Management Endpoints

### Get Documents

Retrieve documents with filtering and pagination.

**Endpoint:** `GET /documents`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `employeeId` (string): Filter by employee
- `type` (string): Filter by document type
- `category` (string): Filter by category
- `status` (string): Filter by status
- `isTemplate` (boolean): Filter templates
- `isConfidential` (boolean): Filter confidential documents
- `requiresApproval` (boolean): Filter approval required
- `search` (string): Search by title or description
- `tags` (string): Filter by tags (comma-separated)
- `page` (number): Page number
- `limit` (number): Items per page

**Response:**
```json
{
  "data": [
    {
      "id": "doc-uuid-1",
      "title": "Employment Contract - John Doe",
      "fileName": "contract_john_doe_2025.pdf",
      "fileSize": 245760,
      "mimeType": "application/pdf",
      "type": "contract",
      "category": "employment",
      "tags": ["contract", "employment", "2025"],
      "status": "active",
      "isConfidential": true,
      "requiresSignature": true,
      "version": 1,
      "employee": {
        "id": "emp-uuid",
        "employeeId": "EMP001",
        "user": {
          "fullName": "John Doe"
        }
      },
      "uploader": {
        "fullName": "HR Manager"
      },
      "approvalStatus": "approved",
      "approvedAt": "2025-01-16T10:00:00Z",
      "uploadedAt": "2025-01-15T14:30:00Z",
      "downloadCount": 3,
      "signatures": [
        {
          "signerUserId": "employee-user-uuid",
          "signedAt": "2025-01-16T09:00:00Z",
          "isValid": true
        }
      ]
    }
  ],
  "total": 125,
  "page": 1,
  "limit": 10,
  "summary": {
    "totalDocuments": 125,
    "pendingApproval": 8,
    "expiringSoon": 3,
    "confidentialDocs": 45
  }
}
```

**Example:**
```bash
curl -X GET "https://api.upsurgemedia.in/documents?employeeId=emp-uuid&type=contract&status=active" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

### Get Document by ID

Retrieve detailed information for a specific document.

**Endpoint:** `GET /documents/{id}`

**Headers:** `Authorization: Bearer <token>`

**Response:**
```json
{
  "data": {
    "id": "doc-uuid-1",
    "title": "Employment Contract - John Doe",
    "fileName": "contract_john_doe_2025.pdf",
    "fileSize": 245760,
    "mimeType": "application/pdf",
    "filePath": "/documents/contracts/2025/contract_john_doe_2025.pdf",
    "type": "contract",
    "category": "employment",
    "tags": ["contract", "employment", "2025"],
    "description": "Standard employment contract for software engineer position",
    "status": "active",
    "isConfidential": true,
    "requiresSignature": true,
    "version": 1,
    "expiryDate": "2026-01-15",
    "employee": {
      "id": "emp-uuid",
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
    "uploader": {
      "id": "hr-user-uuid",
      "fullName": "HR Manager",
      "email": "hr@upsurgemedia.com"
    },
    "approvalStatus": "approved",
    "approvedBy": "hr-manager-uuid",
    "approvedAt": "2025-01-16T10:00:00Z",
    "uploadedAt": "2025-01-15T14:30:00Z",
    "lastAccessedAt": "2025-09-02T15:45:00Z",
    "downloadCount": 3,
    "signatures": [
      {
        "id": "sig-uuid-1",
        "signerUserId": "employee-user-uuid",
        "signedAt": "2025-01-16T09:00:00Z",
        "ipAddress": "192.168.1.100",
        "isValid": true
      },
      {
        "id": "sig-uuid-2", 
        "signerUserId": "hr-manager-uuid",
        "signedAt": "2025-01-16T10:00:00Z",
        "ipAddress": "192.168.1.50",
        "isValid": true
      }
    ],
    "versions": [
      {
        "version": 1,
        "uploadedAt": "2025-01-15T14:30:00Z",
        "uploadedBy": "HR Manager"
      }
    ]
  }
}
```

### Upload Document

Upload a new document.

**Endpoint:** `POST /documents`

**Headers:** `Authorization: Bearer <token>`

**Content-Type:** `multipart/form-data`

**Request Body:**
```
file: <document-file>
title: "Employment Contract - Jane Smith"
type: "contract"
category: "employment"
description: "Employment contract for new hire"
employeeId: "employee-uuid"
isConfidential: true
requiresSignature: true
expiryDate: "2026-03-15"
tags: "contract,employment,2025"
```

**Response:**
```json
{
  "data": {
    "id": "new-doc-uuid",
    "title": "Employment Contract - Jane Smith",
    "fileName": "contract_jane_smith.pdf",
    "fileSize": 198432,
    "mimeType": "application/pdf",
    "type": "contract",
    "category": "employment",
    "status": "draft",
    "isConfidential": true,
    "requiresSignature": true,
    "version": 1,
    "uploadedAt": "2025-09-03T10:00:00Z",
    "approvalStatus": "pending"
  },
  "message": "Document uploaded successfully"
}
```

**Example:**
```bash
curl -X POST "https://api.upsurgemedia.in/documents" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -F "file=@contract.pdf" \
  -F "title=Employment Contract - New Hire" \
  -F "type=contract" \
  -F "employeeId=emp-uuid" \
  -F "isConfidential=true"
```

### Update Document

Update document metadata.

**Endpoint:** `PATCH /documents/{id}`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "title": "Updated Employment Contract - John Doe",
  "description": "Updated contract with new terms",
  "category": "employment-updated",
  "tags": ["contract", "employment", "2025", "updated"],
  "expiryDate": "2027-01-15",
  "status": "active"
}
```

**Response:**
```json
{
  "data": {
    "id": "doc-uuid-1",
    "title": "Updated Employment Contract - John Doe",
    "description": "Updated contract with new terms",
    "category": "employment-updated",
    "tags": ["contract", "employment", "2025", "updated"],
    "expiryDate": "2027-01-15",
    "status": "active",
    "version": 2,
    "updatedAt": "2025-09-03T11:00:00Z"
  },
  "message": "Document updated successfully"
}
```

### Download Document

Download a document file.

**Endpoint:** `GET /documents/{id}/download`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `version` (number): Specific version to download (optional)

**Response:** File download with appropriate headers

**Example:**
```bash
curl -X GET "https://api.upsurgemedia.in/documents/doc-uuid/download" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -o "downloaded_document.pdf"
```

### Delete Document

Soft delete a document.

**Endpoint:** `DELETE /documents/{id}`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `reason` (string): Deletion reason
- `permanent` (boolean): Permanent deletion (Admin only)

**Response:**
```json
{
  "data": {
    "documentId": "doc-uuid",
    "status": "deleted",
    "deletedAt": "2025-09-03T12:00:00Z",
    "deletedBy": "user-uuid",
    "reason": "Document no longer needed"
  },
  "message": "Document deleted successfully"
}
```

## Document Approval

### Get Pending Approvals

Get documents pending approval (Manager/HR only).

**Endpoint:** `GET /documents/pending-approval`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `type` (string): Filter by document type
- `employeeId` (string): Filter by employee
- `urgent` (boolean): Filter urgent approvals

**Response:**
```json
{
  "data": [
    {
      "id": "doc-uuid-2",
      "title": "Performance Review - Q3 2025",
      "type": "review",
      "employee": {
        "employeeId": "EMP002",
        "user": {
          "fullName": "Alice Johnson"
        }
      },
      "uploader": {
        "fullName": "Manager Name"
      },
      "uploadedAt": "2025-09-01T10:00:00Z",
      "daysWaiting": 2,
      "isUrgent": false
    }
  ],
  "total": 8,
  "urgent": 2
}
```

### Approve Document

Approve a document (Manager/HR only).

**Endpoint:** `POST /documents/{id}/approve`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "approvalNotes": "Document reviewed and approved. All terms are acceptable.",
  "conditions": "Employee must sign within 5 business days"
}
```

**Response:**
```json
{
  "data": {
    "documentId": "doc-uuid",
    "approvalStatus": "approved",
    "approvedBy": "manager-uuid",
    "approvedAt": "2025-09-03T14:00:00Z",
    "approvalNotes": "Document reviewed and approved. All terms are acceptable."
  },
  "message": "Document approved successfully"
}
```

### Reject Document

Reject a document (Manager/HR only).

**Endpoint:** `POST /documents/{id}/reject`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "rejectionReason": "Document contains incorrect salary information. Please update and resubmit.",
  "requiredChanges": ["Update salary amount", "Correct start date", "Add missing clauses"]
}
```

**Response:**
```json
{
  "data": {
    "documentId": "doc-uuid",
    "approvalStatus": "rejected",
    "rejectedBy": "manager-uuid",
    "rejectedAt": "2025-09-03T14:00:00Z",
    "rejectionReason": "Document contains incorrect salary information. Please update and resubmit."
  },
  "message": "Document rejected"
}
```

## Digital Signatures

### Sign Document

Add digital signature to a document.

**Endpoint:** `POST /documents/{id}/sign`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "signatureData": "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAA...",
  "signatureType": "digital",
  "agreementText": "I agree to the terms and conditions outlined in this document"
}
```

**Response:**
```json
{
  "data": {
    "signatureId": "sig-uuid",
    "documentId": "doc-uuid",
    "signerUserId": "user-uuid",
    "signedAt": "2025-09-03T15:00:00Z",
    "ipAddress": "192.168.1.100",
    "isValid": true,
    "signatureType": "digital"
  },
  "message": "Document signed successfully"
}
```

### Get Document Signatures

Get all signatures for a document.

**Endpoint:** `GET /documents/{id}/signatures`

**Headers:** `Authorization: Bearer <token>`

**Response:**
```json
{
  "data": [
    {
      "id": "sig-uuid-1",
      "signerUserId": "employee-uuid",
      "signer": {
        "fullName": "John Doe",
        "email": "john.doe@upsurgemedia.com"
      },
      "signedAt": "2025-01-16T09:00:00Z",
      "ipAddress": "192.168.1.100",
      "signatureType": "digital",
      "isValid": true
    },
    {
      "id": "sig-uuid-2",
      "signerUserId": "hr-manager-uuid",
      "signer": {
        "fullName": "HR Manager",
        "email": "hr@upsurgemedia.com"
      },
      "signedAt": "2025-01-16T10:00:00Z",
      "ipAddress": "192.168.1.50",
      "signatureType": "digital",
      "isValid": true
    }
  ],
  "allRequiredSignaturesSigned": true,
  "documentFullyExecuted": true
}
```

### Verify Signature

Verify the validity of a digital signature.

**Endpoint:** `GET /documents/{id}/signatures/{signatureId}/verify`

**Headers:** `Authorization: Bearer <token>`

**Response:**
```json
{
  "data": {
    "signatureId": "sig-uuid",
    "isValid": true,
    "verificationTimestamp": "2025-09-03T16:00:00Z",
    "signatureIntegrity": "intact",
    "documentIntegrity": "intact",
    "certificateStatus": "valid",
    "verificationDetails": {
      "signerIdentity": "verified",
      "timestampAccuracy": "accurate",
      "hashMatch": true
    }
  }
}
```

## Document Templates

### Get Document Templates

Get available document templates.

**Endpoint:** `GET /documents/templates`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `type` (string): Filter by document type
- `isActive` (boolean): Filter by active status

**Response:**
```json
{
  "data": [
    {
      "id": "template-uuid-1",
      "name": "Standard Employment Contract",
      "description": "Standard template for full-time employment contracts",
      "type": "contract",
      "isActive": true,
      "requiresApproval": true,
      "usageCount": 25,
      "fields": [
        {
          "name": "employeeName",
          "label": "Employee Name",
          "type": "text",
          "required": true
        },
        {
          "name": "startDate",
          "label": "Start Date",
          "type": "date",
          "required": true
        },
        {
          "name": "salary",
          "label": "Annual Salary",
          "type": "number",
          "required": true
        }
      ],
      "createdAt": "2025-01-01T00:00:00Z"
    }
  ],
  "total": 12
}
```

### Create Document Template

Create a new document template (Admin/HR only).

**Endpoint:** `POST /documents/templates`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "name": "Performance Review Template",
  "description": "Quarterly performance review template",
  "type": "review",
  "content": "<html><body><h1>Performance Review - {{quarter}} {{year}}</h1><p>Employee: {{employeeName}}</p><p>Manager: {{managerName}}</p><!-- Review content --></body></html>",
  "fields": [
    {
      "name": "employeeName",
      "label": "Employee Name",
      "type": "text",
      "required": true
    },
    {
      "name": "quarter",
      "label": "Quarter",
      "type": "select",
      "options": ["Q1", "Q2", "Q3", "Q4"],
      "required": true
    },
    {
      "name": "year",
      "label": "Year",
      "type": "number",
      "required": true
    }
  ],
  "requiresApproval": true,
  "autoGenerate": false
}
```

**Response:**
```json
{
  "data": {
    "id": "new-template-uuid",
    "name": "Performance Review Template",
    "description": "Quarterly performance review template",
    "type": "review",
    "isActive": true,
    "requiresApproval": true,
    "usageCount": 0,
    "createdAt": "2025-09-03T17:00:00Z"
  },
  "message": "Document template created successfully"
}
```

### Generate Document from Template

Generate a document using a template.

**Endpoint:** `POST /documents/templates/{templateId}/generate`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "employeeId": "employee-uuid",
  "fieldValues": {
    "employeeName": "John Doe",
    "quarter": "Q3",
    "year": 2025,
    "managerName": "Jane Smith"
  },
  "title": "Performance Review Q3 2025 - John Doe",
  "generatePdf": true
}
```

**Response:**
```json
{
  "data": {
    "documentId": "generated-doc-uuid",
    "title": "Performance Review Q3 2025 - John Doe",
    "fileName": "performance_review_q3_2025_john_doe.pdf",
    "status": "draft",
    "generatedAt": "2025-09-03T17:30:00Z",
    "requiresApproval": true
  },
  "message": "Document generated successfully from template"
}
```

## Document Analytics

### Get Document Statistics

Get comprehensive document analytics.

**Endpoint:** `GET /documents/statistics`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `period` (string): Time period (month, quarter, year)
- `type` (string): Filter by document type
- `departmentId` (string): Filter by department

**Response:**
```json
{
  "data": {
    "overview": {
      "totalDocuments": 1250,
      "activeDocuments": 1100,
      "pendingApproval": 25,
      "expiringSoon": 15,
      "confidentialDocs": 450
    },
    "byType": [
      {
        "type": "contract",
        "count": 350,
        "percentage": 28.0
      },
      {
        "type": "policy",
        "count": 200,
        "percentage": 16.0
      }
    ],
    "workflow": {
      "avgApprovalTime": 1.8,
      "pendingApprovals": 25,
      "approvalRate": 94.5,
      "documentsRequiringSignature": 180,
      "signedDocuments": 165
    },
    "usage": {
      "totalDownloads": 5420,
      "avgDownloadsPerDoc": 4.3,
      "mostDownloadedTypes": ["contract", "policy", "form"],
      "templateUsage": {
        "totalGenerated": 145,
        "mostUsedTemplate": "Standard Employment Contract"
      }
    },
    "trends": {
      "uploadTrend": "increasing",
      "monthlyUploads": [
        {
          "month": "2025-01",
          "uploads": 85,
          "approvals": 78
        }
      ]
    }
  }
}
```

## Error Responses

### Common Error Codes

#### 413 Payload Too Large
```json
{
  "error": {
    "code": "FILE_TOO_LARGE",
    "message": "File size exceeds maximum limit of 10MB",
    "details": {
      "maxSize": 10485760,
      "actualSize": 15728640
    },
    "statusCode": 413
  }
}
```

#### 415 Unsupported Media Type
```json
{
  "error": {
    "code": "UNSUPPORTED_FILE_TYPE",
    "message": "File type not supported. Allowed types: PDF, DOC, DOCX, PNG, JPG",
    "details": {
      "providedType": "application/zip",
      "allowedTypes": ["application/pdf", "application/msword", "image/png", "image/jpeg"]
    },
    "statusCode": 415
  }
}
```

#### 403 Forbidden
```json
{
  "error": {
    "code": "CONFIDENTIAL_DOCUMENT_ACCESS_DENIED",
    "message": "Access denied to confidential document",
    "statusCode": 403
  }
}
```

## Integration Examples

### JavaScript/React

```javascript
class DocumentService {
  constructor(baseURL = 'https://api.upsurgemedia.in') {
    this.api = axios.create({ baseURL });
  }

  setToken(token) {
    this.api.defaults.headers.common['Authorization'] = `Bearer ${token}`;
  }

  async getDocuments(params = {}) {
    const response = await this.api.get('/documents', { params });
    return response.data;
  }

  async uploadDocument(file, metadata) {
    const formData = new FormData();
    formData.append('file', file);
    
    Object.keys(metadata).forEach(key => {
      formData.append(key, metadata[key]);
    });

    const response = await this.api.post('/documents', formData, {
      headers: { 'Content-Type': 'multipart/form-data' }
    });
    return response.data.data;
  }

  async downloadDocument(documentId, version = null) {
    const params = version ? { version } : {};
    const response = await this.api.get(`/documents/${documentId}/download`, {
      params,
      responseType: 'blob'
    });
    
    // Create download link
    const url = window.URL.createObjectURL(new Blob([response.data]));
    const link = document.createElement('a');
    link.href = url;
    link.setAttribute('download', this.getFilenameFromResponse(response));
    document.body.appendChild(link);
    link.click();
    link.remove();
    
    return response;
  }

  async signDocument(documentId, signatureData) {
    const response = await this.api.post(`/documents/${documentId}/sign`, {
      signatureData,
      agreementText: "I agree to the terms and conditions"
    });
    return response.data.data;
  }

  async approveDocument(documentId, approvalData) {
    const response = await this.api.post(`/documents/${documentId}/approve`, approvalData);
    return response.data.data;
  }

  async getTemplates(params = {}) {
    const response = await this.api.get('/documents/templates', { params });
    return response.data.data;
  }

  async generateFromTemplate(templateId, data) {
    const response = await this.api.post(`/documents/templates/${templateId}/generate`, data);
    return response.data.data;
  }

  async getPendingApprovals() {
    const response = await this.api.get('/documents/pending-approval');
    return response.data.data;
  }

  getFilenameFromResponse(response) {
    const disposition = response.headers['content-disposition'];
    if (disposition) {
      const filenameMatch = disposition.match(/filename="(.+)"/);
      if (filenameMatch) return filenameMatch[1];
    }
    return 'document';
  }
}

// Usage
const documentService = new DocumentService();
documentService.setToken('your-jwt-token');

// Upload document
const fileInput = document.getElementById('fileInput');
const file = fileInput.files[0];

const uploadedDoc = await documentService.uploadDocument(file, {
  title: 'Employment Contract - New Hire',
  type: 'contract',
  employeeId: 'emp-uuid',
  isConfidential: true,
  requiresSignature: true
});

// Get employee documents
const documents = await documentService.getDocuments({
  employeeId: 'emp-uuid',
  type: 'contract',
  status: 'active'
});

// Sign document
const signature = await documentService.signDocument(
  'doc-uuid',
  'data:image/png;base64,signature-data'
);
```

### Python

```python
import requests
from typing import Dict, List, Optional, BinaryIO

class DocumentAPI:
    def __init__(self, base_url: str = "https://api.upsurgemedia.in"):
        self.base_url = base_url
        self.session = requests.Session()

    def set_token(self, token: str):
        self.session.headers.update({"Authorization": f"Bearer {token}"})

    def get_documents(self, **params) -> Dict:
        response = self.session.get(f"{self.base_url}/documents", params=params)
        response.raise_for_status()
        return response.json()

    def upload_document(self, file_path: str, metadata: Dict) -> Dict:
        with open(file_path, 'rb') as file:
            files = {'file': file}
            response = self.session.post(
                f"{self.base_url}/documents",
                files=files,
                data=metadata
            )
        response.raise_for_status()
        return response.json()["data"]

    def download_document(self, document_id: str, save_path: str, 
                         version: Optional[int] = None) -> bool:
        params = {"version": version} if version else {}
        response = self.session.get(
            f"{self.base_url}/documents/{document_id}/download",
            params=params,
            stream=True
        )
        response.raise_for_status()
        
        with open(save_path, 'wb') as file:
            for chunk in response.iter_content(chunk_size=8192):
                file.write(chunk)
        
        return True

    def sign_document(self, document_id: str, signature_data: str) -> Dict:
        response = self.session.post(
            f"{self.base_url}/documents/{document_id}/sign",
            json={
                "signatureData": signature_data,
                "agreementText": "I agree to the terms and conditions"
            }
        )
        response.raise_for_status()
        return response.json()["data"]

    def approve_document(self, document_id: str, approval_data: Dict) -> Dict:
        response = self.session.post(
            f"{self.base_url}/documents/{document_id}/approve",
            json=approval_data
        )
        response.raise_for_status()
        return response.json()["data"]

    def get_pending_approvals(self, **params) -> List[Dict]:
        response = self.session.get(
            f"{self.base_url}/documents/pending-approval",
            params=params
        )
        response.raise_for_status()
        return response.json()["data"]

    def generate_from_template(self, template_id: str, data: Dict) -> Dict:
        response = self.session.post(
            f"{self.base_url}/documents/templates/{template_id}/generate",
            json=data
        )
        response.raise_for_status()
        return response.json()["data"]

# Usage
api = DocumentAPI()
api.set_token("your-jwt-token")

# Upload contract
uploaded_doc = api.upload_document("contract.pdf", {
    "title": "Employment Contract - New Employee",
    "type": "contract",
    "employeeId": "emp-uuid",
    "isConfidential": "true",
    "requiresSignature": "true",
    "description": "Standard employment contract"
})
print(f"Document uploaded: {uploaded_doc['id']}")

# Get employee documents
documents = api.get_documents(
    employeeId="emp-uuid",
    type="contract",
    status="active"
)
print(f"Found {documents['total']} documents")

# Download document
api.download_document("doc-uuid", "downloaded_contract.pdf")
print("Document downloaded successfully")

# Get pending approvals (for managers)
pending = api.get_pending_approvals(type="contract")
print(f"{len(pending)} documents pending approval")

for doc in pending:
    print(f"- {doc['title']} (waiting {doc['daysWaiting']} days)")
```

---

The Documents API provides comprehensive document lifecycle management with digital signatures, approval workflows, template systems, and secure storage for all organizational documentation needs.
