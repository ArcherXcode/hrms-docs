# 🔔 Notifications API

The Notifications API provides comprehensive notification management including real-time alerts, email notifications, push notifications, and notification preferences with advanced filtering and delivery options.

## Base Endpoint

```
https://api.upsurgemedia.in/api/v1/notifications
```

## Overview

The Notifications API enables organizations to manage all types of notifications across multiple channels, including in-app notifications, emails, SMS, and push notifications with intelligent routing, preferences management, and delivery tracking.

## Notification Models

```typescript
interface Notification {
  id: string;                      // UUID
  title: string;                   // Notification title
  message: string;                 // Notification message
  type: NotificationType;          // Notification type
  category: NotificationCategory;  // Notification category
  
  // Recipient Information
  recipientId: string;             // Recipient user ID
  recipientType: RecipientType;    // User, department, company, role
  companyId: string;               // Company ID
  departmentId?: string;           // Department ID (if applicable)
  
  // Content and Data
  data?: Record<string, any>;      // Additional notification data
  actionUrl?: string;              // Action URL
  imageUrl?: string;               // Notification image
  
  // Delivery Channels
  channels: NotificationChannel[]; // Delivery channels
  priority: NotificationPriority;  // Notification priority
  
  // Status and Tracking
  status: NotificationStatus;      // Sent, delivered, read, failed
  isRead: boolean;                 // Read status
  readAt?: Date;                   // Read timestamp
  deliveredAt?: Date;              // Delivered timestamp
  
  // Scheduling and Expiry
  scheduledFor?: Date;             // Scheduled delivery time
  expiresAt?: Date;                // Expiration time
  retryCount: number;              // Delivery retry count
  maxRetries: number;              // Maximum retry attempts
  
  // Metadata
  tags?: string[];                 // Notification tags
  metadata?: Record<string, any>;  // Additional metadata
  
  // Relationships
  triggeredBy?: string;            // User who triggered notification
  relatedEntity?: {                // Related entity information
    type: string;                  // Entity type (leave, attendance, etc.)
    id: string;                    // Entity ID
  };
  
  // Audit Trail
  createdAt: Date;
  updatedAt: Date;
  
  // Relationships
  recipient?: User;                // Recipient user
  sender?: User;                   // Sender user
  deliveryLogs?: NotificationDeliveryLog[]; // Delivery attempts
}

interface NotificationTemplate {
  id: string;                      // UUID
  name: string;                    // Template name
  description?: string;            // Template description
  type: NotificationType;          // Notification type
  category: NotificationCategory;  // Notification category
  companyId: string;               // Company ID
  
  // Template Content
  title: string;                   // Title template
  message: string;                 // Message template
  emailSubject?: string;           // Email subject template
  emailBody?: string;              // Email body template
  smsMessage?: string;             // SMS message template
  
  // Template Configuration
  channels: NotificationChannel[]; // Default channels
  priority: NotificationPriority;  // Default priority
  variables: TemplateVariable[];   // Template variables
  
  // Settings
  isActive: boolean;               // Template status
  autoSend: boolean;               // Auto-send on trigger
  requiresApproval: boolean;       // Approval required
  
  // Usage Statistics
  usageCount: number;              // Times used
  lastUsed?: Date;                 // Last usage date
  
  // Timestamps
  createdAt: Date;
  updatedAt: Date;
}

interface NotificationPreference {
  id: string;                      // UUID
  userId: string;                  // User ID
  companyId: string;               // Company ID
  
  // Channel Preferences
  channels: {
    inApp: boolean;                // In-app notifications
    email: boolean;                // Email notifications
    sms: boolean;                  // SMS notifications
    push: boolean;                 // Push notifications
  };
  
  // Category Preferences
  categories: {
    [key in NotificationCategory]: {
      enabled: boolean;            // Category enabled
      channels: NotificationChannel[]; // Preferred channels
      priority: NotificationPriority; // Priority threshold
    };
  };
  
  // Schedule Preferences
  schedule: {
    workingHours: {
      enabled: boolean;            // Only during working hours
      start: string;               // Start time (HH:mm)
      end: string;                 // End time (HH:mm)
    };
    doNotDisturb: {
      enabled: boolean;            // Do not disturb mode
      start: string;               // DND start time
      end: string;                 // DND end time
    };
    weekends: boolean;             // Allow weekend notifications
  };
  
  // Advanced Settings
  digest: {
    enabled: boolean;              // Enable digest notifications
    frequency: DigestFrequency;    // Digest frequency
    time: string;                  // Digest delivery time
  };
  
  // Timestamps
  createdAt: Date;
  updatedAt: Date;
}

interface NotificationDeliveryLog {
  id: string;                      // UUID
  notificationId: string;          // Notification ID
  channel: NotificationChannel;    // Delivery channel
  status: DeliveryStatus;          // Delivery status
  
  // Delivery Details
  attemptedAt: Date;               // Delivery attempt time
  deliveredAt?: Date;              // Actual delivery time
  failureReason?: string;          // Failure reason
  retryCount: number;              // Retry attempt number
  
  // Provider Information
  provider?: string;               // Service provider (SendGrid, Twilio, etc.)
  providerId?: string;             // Provider message ID
  providerResponse?: any;          // Provider response data
  
  // Tracking
  opened?: boolean;                // Email/SMS opened
  clicked?: boolean;               // Link clicked
  openedAt?: Date;                 // Opened timestamp
  clickedAt?: Date;                // Clicked timestamp
  
  // Timestamps
  createdAt: Date;
  updatedAt: Date;
}

enum NotificationType {
  SYSTEM = 'system',
  ATTENDANCE = 'attendance',
  LEAVE = 'leave',
  PAYROLL = 'payroll',
  EMPLOYEE = 'employee',
  DEPARTMENT = 'department',
  DOCUMENT = 'document',
  PERFORMANCE = 'performance',
  TRAINING = 'training',
  ANNOUNCEMENT = 'announcement',
  REMINDER = 'reminder',
  ALERT = 'alert'
}

enum NotificationCategory {
  URGENT = 'urgent',
  APPROVAL_REQUIRED = 'approval_required',
  INFORMATION = 'information',
  REMINDER = 'reminder',
  SYSTEM_UPDATE = 'system_update',
  SECURITY = 'security',
  MARKETING = 'marketing',
  PERSONAL = 'personal'
}

enum NotificationChannel {
  IN_APP = 'in_app',
  EMAIL = 'email',
  SMS = 'sms',
  PUSH = 'push',
  SLACK = 'slack',
  TEAMS = 'teams',
  WEBHOOK = 'webhook'
}

enum NotificationPriority {
  LOW = 'low',
  MEDIUM = 'medium',
  HIGH = 'high',
  URGENT = 'urgent'
}

enum NotificationStatus {
  PENDING = 'pending',
  QUEUED = 'queued',
  SENDING = 'sending',
  SENT = 'sent',
  DELIVERED = 'delivered',
  READ = 'read',
  FAILED = 'failed',
  EXPIRED = 'expired'
}

enum RecipientType {
  USER = 'user',
  DEPARTMENT = 'department',
  ROLE = 'role',
  COMPANY = 'company',
  CUSTOM_GROUP = 'custom_group'
}

enum DigestFrequency {
  DAILY = 'daily',
  WEEKLY = 'weekly',
  MONTHLY = 'monthly'
}

enum DeliveryStatus {
  PENDING = 'pending',
  SENT = 'sent',
  DELIVERED = 'delivered',
  FAILED = 'failed',
  BOUNCED = 'bounced',
  CLICKED = 'clicked',
  OPENED = 'opened'
}
```

## Notification Management

### Get Notifications

Retrieve notifications for the authenticated user.

**Endpoint:** `GET /notifications`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `type` (string): Filter by notification type
- `category` (string): Filter by category
- `priority` (string): Filter by priority
- `status` (string): Filter by status
- `isRead` (boolean): Filter by read status
- `channel` (string): Filter by delivery channel
- `startDate` (string): Start date filter (ISO 8601)
- `endDate` (string): End date filter (ISO 8601)
- `search` (string): Search in title/message
- `page` (number): Page number
- `limit` (number): Items per page

**Response:**
```json
{
  "data": [
    {
      "id": "notif-uuid-1",
      "title": "Leave Request Approved",
      "message": "Your leave request for September 15-16 has been approved by your manager.",
      "type": "leave",
      "category": "approval_required",
      "priority": "medium",
      "status": "delivered",
      "isRead": false,
      "channels": ["in_app", "email"],
      "actionUrl": "/leave/requests/req-uuid",
      "data": {
        "leaveRequestId": "req-uuid",
        "startDate": "2025-09-15",
        "endDate": "2025-09-16",
        "approvedBy": "Manager Name"
      },
      "relatedEntity": {
        "type": "leave_request",
        "id": "req-uuid"
      },
      "deliveredAt": "2025-09-03T09:30:00Z",
      "createdAt": "2025-09-03T09:25:00Z",
      "sender": {
        "id": "manager-uuid",
        "fullName": "Sarah Johnson",
        "role": "Manager"
      }
    },
    {
      "id": "notif-uuid-2",
      "title": "Late Check-in Alert",
      "message": "You checked in 15 minutes late today. Please ensure punctual attendance.",
      "type": "attendance",
      "category": "reminder",
      "priority": "low",
      "status": "read",
      "isRead": true,
      "channels": ["in_app"],
      "readAt": "2025-09-03T10:15:00Z",
      "deliveredAt": "2025-09-03T09:15:00Z",
      "createdAt": "2025-09-03T09:15:00Z",
      "data": {
        "checkInTime": "2025-09-03T09:15:00Z",
        "expectedTime": "2025-09-03T09:00:00Z",
        "lateMinutes": 15
      }
    }
  ],
  "total": 45,
  "unread": 12,
  "page": 1,
  "limit": 10,
  "summary": {
    "totalNotifications": 45,
    "unreadCount": 12,
    "urgentCount": 2,
    "todayCount": 8,
    "byCategory": {
      "urgent": 2,
      "approval_required": 5,
      "information": 15,
      "reminder": 23
    }
  }
}
```

**Example:**
```bash
curl -X GET "https://api.upsurgemedia.in/api/v1/notifications?isRead=false&priority=high" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

### Get Notification by ID

Retrieve detailed information for a specific notification.

**Endpoint:** `GET /notifications/{id}`

**Headers:** `Authorization: Bearer <token>`

**Response:**
```json
{
  "data": {
    "id": "notif-uuid-1",
    "title": "Monthly Performance Review Due",
    "message": "Your monthly performance review is due. Please complete your self-assessment by September 10.",
    "type": "performance",
    "category": "reminder",
    "priority": "medium",
    "status": "delivered",
    "isRead": false,
    "channels": ["in_app", "email", "push"],
    "actionUrl": "/performance/self-assessment",
    "imageUrl": "https://assets.upsurgemedia.in/notifications/performance-review.png",
    "data": {
      "reviewPeriod": "September 2025",
      "dueDate": "2025-09-10",
      "reviewType": "monthly",
      "managerId": "manager-uuid"
    },
    "tags": ["performance", "monthly", "reminder"],
    "expiresAt": "2025-09-10T23:59:59Z",
    "relatedEntity": {
      "type": "performance_review",
      "id": "review-uuid"
    },
    "recipient": {
      "id": "user-uuid",
      "fullName": "John Doe",
      "email": "john.doe@upsurgemedia.com"
    },
    "sender": {
      "id": "system-uuid",
      "fullName": "System",
      "role": "System"
    },
    "deliveryLogs": [
      {
        "id": "log-uuid-1",
        "channel": "email",
        "status": "delivered",
        "attemptedAt": "2025-09-03T08:00:00Z",
        "deliveredAt": "2025-09-03T08:00:15Z",
        "provider": "SendGrid",
        "providerId": "sg-msg-12345"
      },
      {
        "id": "log-uuid-2",
        "channel": "push",
        "status": "delivered",
        "attemptedAt": "2025-09-03T08:00:00Z",
        "deliveredAt": "2025-09-03T08:00:10Z",
        "provider": "FCM",
        "clicked": true,
        "clickedAt": "2025-09-03T08:30:00Z"
      }
    ],
    "createdAt": "2025-09-03T08:00:00Z",
    "updatedAt": "2025-09-03T08:30:00Z"
  }
}
```

### Mark as Read

Mark one or more notifications as read.

**Endpoint:** `PATCH /notifications/mark-read`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "notificationIds": ["notif-uuid-1", "notif-uuid-2"],
  "markAll": false
}
```

**Response:**
```json
{
  "data": {
    "markedCount": 2,
    "notificationIds": ["notif-uuid-1", "notif-uuid-2"],
    "markedAt": "2025-09-03T10:30:00Z"
  },
  "message": "Notifications marked as read successfully"
}
```

### Mark All as Read

Mark all notifications as read for the authenticated user.

**Endpoint:** `PATCH /notifications/mark-all-read`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `type` (string): Mark all of specific type
- `category` (string): Mark all of specific category

**Response:**
```json
{
  "data": {
    "markedCount": 12,
    "markedAt": "2025-09-03T10:30:00Z"
  },
  "message": "All notifications marked as read"
}
```

### Delete Notification

Delete a notification (soft delete).

**Endpoint:** `DELETE /notifications/{id}`

**Headers:** `Authorization: Bearer <token>`

**Response:**
```json
{
  "data": {
    "notificationId": "notif-uuid",
    "deletedAt": "2025-09-03T11:00:00Z"
  },
  "message": "Notification deleted successfully"
}
```

## Send Notifications

### Send Single Notification

Send a notification to a specific user or group.

**Endpoint:** `POST /notifications/send`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "title": "Important Announcement",
  "message": "Please attend the all-hands meeting tomorrow at 10 AM in the main conference room.",
  "type": "announcement",
  "category": "information",
  "priority": "high",
  "channels": ["in_app", "email", "push"],
  "recipients": [
    {
      "type": "user",
      "id": "user-uuid-1"
    },
    {
      "type": "department",
      "id": "dept-uuid-1"
    }
  ],
  "actionUrl": "/meetings/all-hands-2025-09-04",
  "data": {
    "meetingId": "meeting-uuid",
    "meetingDate": "2025-09-04T10:00:00Z",
    "location": "Main Conference Room"
  },
  "scheduledFor": "2025-09-04T08:00:00Z",
  "expiresAt": "2025-09-04T10:00:00Z"
}
```

**Response:**
```json
{
  "data": {
    "notificationId": "new-notif-uuid",
    "status": "queued",
    "recipientCount": 125,
    "scheduledFor": "2025-09-04T08:00:00Z",
    "estimatedDelivery": "2025-09-04T08:00:15Z"
  },
  "message": "Notification queued for delivery"
}
```

### Send Bulk Notifications

Send notifications to multiple recipients with different content.

**Endpoint:** `POST /notifications/send-bulk`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "notifications": [
    {
      "title": "Welcome to the Team",
      "message": "Welcome aboard! Your first day orientation is scheduled for tomorrow.",
      "type": "employee",
      "category": "information",
      "priority": "medium",
      "channels": ["in_app", "email"],
      "recipientId": "new-employee-uuid",
      "data": {
        "startDate": "2025-09-04",
        "orientation": true
      }
    },
    {
      "title": "Performance Review Reminder",
      "message": "Your quarterly performance review is due next week.",
      "type": "performance",
      "category": "reminder",
      "priority": "medium",
      "channels": ["in_app", "email"],
      "recipientId": "employee-uuid-2",
      "data": {
        "reviewType": "quarterly",
        "dueDate": "2025-09-10"
      }
    }
  ]
}
```

**Response:**
```json
{
  "data": {
    "totalNotifications": 2,
    "queued": 2,
    "failed": 0,
    "notifications": [
      {
        "id": "bulk-notif-uuid-1",
        "status": "queued",
        "recipientId": "new-employee-uuid"
      },
      {
        "id": "bulk-notif-uuid-2",
        "status": "queued",
        "recipientId": "employee-uuid-2"
      }
    ]
  },
  "message": "Bulk notifications queued successfully"
}
```

### Send from Template

Send notification using a predefined template.

**Endpoint:** `POST /notifications/send-template`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "templateId": "template-uuid",
  "recipients": [
    {
      "type": "user",
      "id": "user-uuid"
    }
  ],
  "variables": {
    "employeeName": "John Doe",
    "leaveStartDate": "September 15, 2025",
    "leaveEndDate": "September 16, 2025",
    "approverName": "Sarah Johnson"
  },
  "priority": "medium",
  "scheduledFor": "2025-09-03T14:00:00Z"
}
```

**Response:**
```json
{
  "data": {
    "notificationId": "template-notif-uuid",
    "templateId": "template-uuid",
    "status": "queued",
    "recipientCount": 1,
    "renderedContent": {
      "title": "Leave Request Approved - John Doe",
      "message": "Your leave request from September 15, 2025 to September 16, 2025 has been approved by Sarah Johnson."
    }
  },
  "message": "Template notification queued successfully"
}
```

## Notification Templates

### Get Templates

Retrieve notification templates.

**Endpoint:** `GET /notifications/templates`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `type` (string): Filter by notification type
- `category` (string): Filter by category
- `isActive` (boolean): Filter by active status

**Response:**
```json
{
  "data": [
    {
      "id": "template-uuid-1",
      "name": "Leave Request Approval",
      "description": "Template for leave request approval notifications",
      "type": "leave",
      "category": "approval_required",
      "title": "Leave Request {{status}} - {{employeeName}}",
      "message": "Your leave request from {{leaveStartDate}} to {{leaveEndDate}} has been {{status}} by {{approverName}}.",
      "emailSubject": "Leave Request {{status}}",
      "channels": ["in_app", "email"],
      "priority": "medium",
      "variables": [
        {
          "name": "employeeName",
          "type": "string",
          "required": true,
          "description": "Employee full name"
        },
        {
          "name": "leaveStartDate",
          "type": "date",
          "required": true,
          "description": "Leave start date"
        },
        {
          "name": "status",
          "type": "enum",
          "options": ["approved", "rejected"],
          "required": true,
          "description": "Approval status"
        }
      ],
      "isActive": true,
      "usageCount": 156,
      "lastUsed": "2025-09-03T10:00:00Z",
      "createdAt": "2025-01-01T00:00:00Z"
    }
  ],
  "total": 25
}
```

### Create Template

Create a new notification template.

**Endpoint:** `POST /notifications/templates`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "name": "Employee Birthday Reminder",
  "description": "Template for employee birthday notifications",
  "type": "employee",
  "category": "information",
  "title": "🎉 Happy Birthday {{employeeName}}!",
  "message": "Today is {{employeeName}}'s birthday! Join us in wishing them a wonderful year ahead.",
  "emailSubject": "Birthday Wishes - {{employeeName}}",
  "emailBody": "<html><body><h2>🎉 Happy Birthday {{employeeName}}!</h2><p>Today is a special day as we celebrate {{employeeName}}'s birthday!</p><p>Team at {{companyName}}</p></body></html>",
  "channels": ["in_app", "email"],
  "priority": "low",
  "variables": [
    {
      "name": "employeeName",
      "type": "string",
      "required": true,
      "description": "Employee full name"
    },
    {
      "name": "companyName",
      "type": "string",
      "required": true,
      "description": "Company name"
    }
  ],
  "autoSend": true,
  "isActive": true
}
```

**Response:**
```json
{
  "data": {
    "id": "new-template-uuid",
    "name": "Employee Birthday Reminder",
    "type": "employee",
    "category": "information",
    "isActive": true,
    "createdAt": "2025-09-03T12:00:00Z"
  },
  "message": "Notification template created successfully"
}
```

### Update Template

Update an existing notification template.

**Endpoint:** `PATCH /notifications/templates/{id}`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "title": "🎂 Happy Birthday {{employeeName}}!",
  "message": "Wishing {{employeeName}} a very happy birthday and another year of success!",
  "priority": "medium",
  "isActive": true
}
```

**Response:**
```json
{
  "data": {
    "templateId": "template-uuid",
    "updatedFields": ["title", "message", "priority"],
    "updatedAt": "2025-09-03T12:30:00Z"
  },
  "message": "Template updated successfully"
}
```

## Notification Preferences

### Get User Preferences

Get notification preferences for the authenticated user.

**Endpoint:** `GET /notifications/preferences`

**Headers:** `Authorization: Bearer <token>`

**Response:**
```json
{
  "data": {
    "id": "pref-uuid",
    "userId": "user-uuid",
    "channels": {
      "inApp": true,
      "email": true,
      "sms": false,
      "push": true
    },
    "categories": {
      "urgent": {
        "enabled": true,
        "channels": ["in_app", "email", "push"],
        "priority": "urgent"
      },
      "approval_required": {
        "enabled": true,
        "channels": ["in_app", "email"],
        "priority": "high"
      },
      "information": {
        "enabled": true,
        "channels": ["in_app"],
        "priority": "medium"
      },
      "reminder": {
        "enabled": true,
        "channels": ["in_app", "push"],
        "priority": "low"
      },
      "marketing": {
        "enabled": false,
        "channels": [],
        "priority": "low"
      }
    },
    "schedule": {
      "workingHours": {
        "enabled": true,
        "start": "09:00",
        "end": "18:00"
      },
      "doNotDisturb": {
        "enabled": true,
        "start": "22:00",
        "end": "07:00"
      },
      "weekends": false
    },
    "digest": {
      "enabled": true,
      "frequency": "daily",
      "time": "08:00"
    },
    "updatedAt": "2025-09-03T10:00:00Z"
  }
}
```

### Update Preferences

Update notification preferences for the authenticated user.

**Endpoint:** `PATCH /notifications/preferences`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "channels": {
    "inApp": true,
    "email": true,
    "sms": true,
    "push": true
  },
  "categories": {
    "urgent": {
      "enabled": true,
      "channels": ["in_app", "email", "sms", "push"],
      "priority": "urgent"
    },
    "marketing": {
      "enabled": false,
      "channels": [],
      "priority": "low"
    }
  },
  "schedule": {
    "workingHours": {
      "enabled": false
    },
    "doNotDisturb": {
      "enabled": true,
      "start": "23:00",
      "end": "06:00"
    }
  },
  "digest": {
    "enabled": true,
    "frequency": "weekly",
    "time": "09:00"
  }
}
```

**Response:**
```json
{
  "data": {
    "preferenceId": "pref-uuid",
    "updatedFields": ["channels", "categories", "schedule", "digest"],
    "updatedAt": "2025-09-03T13:00:00Z"
  },
  "message": "Notification preferences updated successfully"
}
```

## Analytics and Reporting

### Get Notification Analytics

Get comprehensive notification analytics and metrics.

**Endpoint:** `GET /notifications/analytics`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `period` (string): Analysis period (week, month, quarter, year)
- `type` (string): Filter by notification type
- `channel` (string): Filter by delivery channel

**Response:**
```json
{
  "data": {
    "overview": {
      "totalNotifications": 15420,
      "deliveredNotifications": 14890,
      "readNotifications": 12650,
      "failedNotifications": 530,
      "deliveryRate": 96.6,
      "readRate": 82.1,
      "avgDeliveryTime": 2.3
    },
    "byChannel": [
      {
        "channel": "in_app",
        "sent": 15420,
        "delivered": 15420,
        "read": 12650,
        "deliveryRate": 100.0,
        "readRate": 82.1
      },
      {
        "channel": "email",
        "sent": 8920,
        "delivered": 8456,
        "opened": 3245,
        "clicked": 1567,
        "deliveryRate": 94.8,
        "openRate": 36.4,
        "clickRate": 17.6
      },
      {
        "channel": "push",
        "sent": 6500,
        "delivered": 6014,
        "opened": 2890,
        "deliveryRate": 92.5,
        "openRate": 44.5
      }
    ],
    "byType": [
      {
        "type": "attendance",
        "sent": 4560,
        "delivered": 4456,
        "read": 3890,
        "readRate": 85.3
      },
      {
        "type": "leave",
        "sent": 2340,
        "delivered": 2298,
        "read": 2156,
        "readRate": 92.1
      }
    ],
    "byCategory": [
      {
        "category": "urgent",
        "sent": 156,
        "delivered": 156,
        "read": 148,
        "avgReadTime": 0.8
      }
    ],
    "trends": {
      "daily": [
        {
          "date": "2025-09-01",
          "sent": 520,
          "delivered": 506,
          "read": 445
        }
      ],
      "hourly": [
        {
          "hour": 9,
          "sent": 145,
          "delivered": 143,
          "read": 125
        }
      ]
    },
    "engagement": {
      "topPerformingTypes": ["leave", "payroll", "urgent"],
      "leastEngagingTypes": ["marketing", "system_update"],
      "peakHours": [9, 10, 14, 15],
      "avgTimeToRead": 1.5
    }
  }
}
```

### Get Delivery Reports

Get detailed delivery reports for notifications.

**Endpoint:** `GET /notifications/delivery-reports`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `notificationId` (string): Specific notification ID
- `startDate` (string): Start date filter
- `endDate` (string): End date filter
- `status` (string): Filter by delivery status
- `channel` (string): Filter by channel

**Response:**
```json
{
  "data": [
    {
      "notificationId": "notif-uuid-1",
      "title": "Leave Request Approved",
      "totalRecipients": 1,
      "deliveryLogs": [
        {
          "channel": "email",
          "status": "delivered",
          "attemptedAt": "2025-09-03T09:00:00Z",
          "deliveredAt": "2025-09-03T09:00:15Z",
          "provider": "SendGrid",
          "opened": true,
          "openedAt": "2025-09-03T09:30:00Z"
        },
        {
          "channel": "push",
          "status": "delivered",
          "attemptedAt": "2025-09-03T09:00:00Z",
          "deliveredAt": "2025-09-03T09:00:10Z",
          "provider": "FCM"
        }
      ]
    }
  ],
  "summary": {
    "totalNotifications": 1,
    "totalDeliveries": 2,
    "successfulDeliveries": 2,
    "failedDeliveries": 0,
    "overallDeliveryRate": 100.0
  }
}
```

## Real-time Notifications

### WebSocket Connection

Connect to real-time notification feed.

**Endpoint:** `wss://api.upsurgemedia.in/api/v1/notifications/live`

**Headers:** `Authorization: Bearer <token>`

**Connection Example:**
```javascript
const ws = new WebSocket('wss://api.upsurgemedia.in/api/v1/notifications/live?token=YOUR_TOKEN');

ws.onopen = function() {
  console.log('Connected to notification feed');
};

ws.onmessage = function(event) {
  const notification = JSON.parse(event.data);
  
  switch(notification.type) {
    case 'new_notification':
      displayNotification(notification.data);
      break;
    case 'notification_read':
      markAsRead(notification.notificationId);
      break;
    case 'batch_update':
      updateNotificationCount(notification.unreadCount);
      break;
  }
};

ws.onerror = function(error) {
  console.error('WebSocket error:', error);
};
```

### Server-Sent Events

Alternative real-time connection using SSE.

**Endpoint:** `GET /notifications/events`

**Headers:** `Authorization: Bearer <token>`

**Example:**
```javascript
const eventSource = new EventSource('https://api.upsurgemedia.in/api/v1/notifications/events?token=YOUR_TOKEN');

eventSource.onmessage = function(event) {
  const data = JSON.parse(event.data);
  handleNotificationUpdate(data);
};

eventSource.addEventListener('new_notification', function(event) {
  const notification = JSON.parse(event.data);
  showInAppNotification(notification);
});

eventSource.addEventListener('unread_count', function(event) {
  const data = JSON.parse(event.data);
  updateUnreadBadge(data.count);
});
```

## Integration Examples

### JavaScript/React Notification System

```javascript
class NotificationService {
  constructor(baseURL = 'https://api.upsurgemedia.in') {
    this.api = axios.create({ baseURL });
    this.ws = null;
    this.eventSource = null;
    this.listeners = new Map();
  }

  setToken(token) {
    this.api.defaults.headers.common['Authorization'] = `Bearer ${token}`;
    this.token = token;
  }

  async getNotifications(params = {}) {
    const response = await this.api.get('/notifications', { params });
    return response.data;
  }

  async markAsRead(notificationIds) {
    const response = await this.api.patch('/notifications/mark-read', {
      notificationIds: Array.isArray(notificationIds) ? notificationIds : [notificationIds]
    });
    return response.data;
  }

  async markAllAsRead() {
    const response = await this.api.patch('/notifications/mark-all-read');
    return response.data;
  }

  async sendNotification(notification) {
    const response = await this.api.post('/notifications/send', notification);
    return response.data.data;
  }

  async getPreferences() {
    const response = await this.api.get('/notifications/preferences');
    return response.data.data;
  }

  async updatePreferences(preferences) {
    const response = await this.api.patch('/notifications/preferences', preferences);
    return response.data.data;
  }

  async getTemplates(params = {}) {
    const response = await this.api.get('/notifications/templates', { params });
    return response.data.data;
  }

  async sendFromTemplate(templateId, data) {
    const response = await this.api.post('/notifications/send-template', {
      templateId,
      ...data
    });
    return response.data.data;
  }

  // Real-time connection
  connectRealtime(options = {}) {
    const { useWebSocket = true } = options;
    
    if (useWebSocket) {
      this.connectWebSocket();
    } else {
      this.connectSSE();
    }
  }

  connectWebSocket() {
    const wsUrl = `wss://api.upsurgemedia.in/api/v1/notifications/live?token=${this.token}`;
    this.ws = new WebSocket(wsUrl);
    
    this.ws.onopen = () => {
      this.emit('connected');
    };
    
    this.ws.onmessage = (event) => {
      const data = JSON.parse(event.data);
      this.handleRealtimeUpdate(data);
    };
    
    this.ws.onerror = (error) => {
      this.emit('error', error);
    };
    
    this.ws.onclose = () => {
      this.emit('disconnected');
      // Auto-reconnect logic
      setTimeout(() => this.connectWebSocket(), 3000);
    };
  }

  connectSSE() {
    const sseUrl = `https://api.upsurgemedia.in/api/v1/notifications/events?token=${this.token}`;
    this.eventSource = new EventSource(sseUrl);
    
    this.eventSource.onopen = () => {
      this.emit('connected');
    };
    
    this.eventSource.onmessage = (event) => {
      const data = JSON.parse(event.data);
      this.handleRealtimeUpdate(data);
    };
    
    this.eventSource.onerror = (error) => {
      this.emit('error', error);
    };
  }

  handleRealtimeUpdate(data) {
    switch (data.type) {
      case 'new_notification':
        this.emit('notification', data.notification);
        break;
      case 'notification_read':
        this.emit('read', data.notificationId);
        break;
      case 'unread_count':
        this.emit('unreadCount', data.count);
        break;
    }
  }

  disconnect() {
    if (this.ws) {
      this.ws.close();
      this.ws = null;
    }
    if (this.eventSource) {
      this.eventSource.close();
      this.eventSource = null;
    }
  }

  // Event system
  on(event, callback) {
    if (!this.listeners.has(event)) {
      this.listeners.set(event, []);
    }
    this.listeners.get(event).push(callback);
  }

  off(event, callback) {
    if (this.listeners.has(event)) {
      const callbacks = this.listeners.get(event);
      const index = callbacks.indexOf(callback);
      if (index > -1) {
        callbacks.splice(index, 1);
      }
    }
  }

  emit(event, data) {
    if (this.listeners.has(event)) {
      this.listeners.get(event).forEach(callback => callback(data));
    }
  }
}

// Usage
const notificationService = new NotificationService();
notificationService.setToken('your-jwt-token');

// Get notifications
const notifications = await notificationService.getNotifications({
  isRead: false,
  limit: 20
});

// Connect to real-time updates
notificationService.connectRealtime();

notificationService.on('notification', (notification) => {
  console.log('New notification:', notification);
  showToast(notification.title, notification.message);
});

notificationService.on('unreadCount', (count) => {
  updateUnreadBadge(count);
});

// Send notification
const sent = await notificationService.sendNotification({
  title: 'Team Meeting',
  message: 'Daily standup in 5 minutes',
  type: 'announcement',
  category: 'information',
  priority: 'medium',
  channels: ['in_app', 'push'],
  recipients: [{ type: 'department', id: 'dept-uuid' }]
});
```

### Python Notification Management

```python
import requests
import json
from typing import Dict, List, Optional
from datetime import datetime

class NotificationAPI:
    def __init__(self, base_url: str = "https://api.upsurgemedia.in"):
        self.base_url = base_url
        self.session = requests.Session()

    def set_token(self, token: str):
        self.session.headers.update({"Authorization": f"Bearer {token}"})

    def get_notifications(self, **params) -> Dict:
        response = self.session.get(f"{self.base_url}/notifications", params=params)
        response.raise_for_status()
        return response.json()

    def send_notification(self, notification_data: Dict) -> Dict:
        response = self.session.post(
            f"{self.base_url}/notifications/send",
            json=notification_data
        )
        response.raise_for_status()
        return response.json()["data"]

    def send_bulk_notifications(self, notifications: List[Dict]) -> Dict:
        response = self.session.post(
            f"{self.base_url}/notifications/send-bulk",
            json={"notifications": notifications}
        )
        response.raise_for_status()
        return response.json()["data"]

    def mark_as_read(self, notification_ids: List[str]) -> Dict:
        response = self.session.patch(
            f"{self.base_url}/notifications/mark-read",
            json={"notificationIds": notification_ids}
        )
        response.raise_for_status()
        return response.json()["data"]

    def get_analytics(self, **params) -> Dict:
        response = self.session.get(
            f"{self.base_url}/notifications/analytics",
            params=params
        )
        response.raise_for_status()
        return response.json()["data"]

    def create_template(self, template_data: Dict) -> Dict:
        response = self.session.post(
            f"{self.base_url}/notifications/templates",
            json=template_data
        )
        response.raise_for_status()
        return response.json()["data"]

    def send_from_template(self, template_id: str, data: Dict) -> Dict:
        response = self.session.post(
            f"{self.base_url}/notifications/send-template",
            json={"templateId": template_id, **data}
        )
        response.raise_for_status()
        return response.json()["data"]

    # Helper methods for common notification scenarios
    def send_leave_approval(self, employee_id: str, leave_details: Dict) -> Dict:
        return self.send_notification({
            "title": f"Leave Request Approved",
            "message": f"Your leave request from {leave_details['startDate']} to {leave_details['endDate']} has been approved.",
            "type": "leave",
            "category": "approval_required",
            "priority": "medium",
            "channels": ["in_app", "email"],
            "recipients": [{"type": "user", "id": employee_id}],
            "data": leave_details
        })

    def send_attendance_reminder(self, employee_ids: List[str]) -> Dict:
        notifications = []
        for emp_id in employee_ids:
            notifications.append({
                "title": "Attendance Reminder",
                "message": "Please remember to check in when you arrive at the office.",
                "type": "attendance",
                "category": "reminder",
                "priority": "low",
                "channels": ["in_app", "push"],
                "recipientId": emp_id
            })
        
        return self.send_bulk_notifications(notifications)

    def send_payroll_notification(self, employee_ids: List[str], payroll_date: str) -> Dict:
        notifications = []
        for emp_id in employee_ids:
            notifications.append({
                "title": "Payroll Processed",
                "message": f"Your salary for this month has been processed and will be credited on {payroll_date}.",
                "type": "payroll",
                "category": "information",
                "priority": "medium",
                "channels": ["in_app", "email"],
                "recipientId": emp_id,
                "data": {"payrollDate": payroll_date}
            })
        
        return self.send_bulk_notifications(notifications)

    def generate_notification_report(self, period: str = "month") -> Dict:
        analytics = self.get_analytics(period=period)
        
        report = {
            "period": period,
            "summary": analytics["overview"],
            "performance": {
                "delivery_rate": analytics["overview"]["deliveryRate"],
                "read_rate": analytics["overview"]["readRate"],
                "engagement": analytics["engagement"]
            },
            "channel_performance": analytics["byChannel"],
            "type_performance": analytics["byType"],
            "recommendations": []
        }
        
        # Generate recommendations
        if analytics["overview"]["deliveryRate"] < 95:
            report["recommendations"].append("Delivery rate below target - review notification channels")
        
        if analytics["overview"]["readRate"] < 70:
            report["recommendations"].append("Low read rate - consider improving notification content")
        
        # Find best performing channel
        best_channel = max(analytics["byChannel"], key=lambda x: x["readRate"])
        report["recommendations"].append(f"Best performing channel: {best_channel['channel']} with {best_channel['readRate']}% read rate")
        
        return report

# Usage
api = NotificationAPI()
api.set_token("your-jwt-token")

# Send a simple notification
notification = api.send_notification({
    "title": "System Maintenance",
    "message": "The system will be down for maintenance from 2 AM to 4 AM tonight.",
    "type": "system",
    "category": "urgent",
    "priority": "high",
    "channels": ["in_app", "email", "push"],
    "recipients": [{"type": "company", "id": "company-uuid"}]
})
print(f"Notification sent: {notification['notificationId']}")

# Send leave approval
leave_approval = api.send_leave_approval("employee-uuid", {
    "startDate": "2025-09-15",
    "endDate": "2025-09-16",
    "leaveType": "annual",
    "approvedBy": "Manager Name"
})
print("Leave approval notification sent")

# Get notification analytics
analytics = api.get_analytics(period="month")
print(f"Total notifications: {analytics['overview']['totalNotifications']}")
print(f"Delivery rate: {analytics['overview']['deliveryRate']}%")
print(f"Read rate: {analytics['overview']['readRate']}%")

# Generate comprehensive report
report = api.generate_notification_report("month")
print("\nNotification Performance Report:")
print(f"Period: {report['period']}")
print(f"Delivery Rate: {report['performance']['delivery_rate']}%")
print(f"Read Rate: {report['performance']['read_rate']}%")

print("\nRecommendations:")
for rec in report['recommendations']:
    print(f"- {rec}")
```

---

The Notifications API provides a comprehensive, multi-channel notification system with intelligent routing, user preferences, template management, and detailed analytics for effective organizational communication.
