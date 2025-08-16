# Notifications API

The Notifications API manages system notifications, alerts, messaging, and communication workflows within the HRMS system.

## Endpoints Overview

| Method | Endpoint | Description | Access |
|--------|----------|-------------|---------|
| GET | `/notifications` | List notifications | All authenticated |
| GET | `/notifications/:id` | Get notification details | Owner, HR, Admin |
| POST | `/notifications` | Create notification | HR, Admin |
| PUT | `/notifications/:id` | Update notification | HR, Admin |
| DELETE | `/notifications/:id` | Delete notification | HR, Admin |
| POST | `/notifications/:id/read` | Mark notification as read | Owner |
| POST | `/notifications/bulk-read` | Mark multiple as read | Owner |
| GET | `/notifications/unread/count` | Get unread count | All authenticated |
| POST | `/notifications/send` | Send notification | HR, Admin |
| GET | `/notifications/templates` | Get notification templates | HR, Admin |
| GET | `/notifications/stats/overview` | Get notification statistics | HR, Admin |

## GET /notifications

Retrieve notifications with filtering and pagination.

### Request

```http
GET /api/notifications?page=1&limit=20&type=leave&status=unread&priority=high
Authorization: Bearer <token>
```

### Query Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| page | number | 1 | Page number for pagination |
| limit | number | 20 | Number of records per page |
| type | string | - | Filter by type (leave, expense, timesheet, system, announcement) |
| status | string | - | Filter by status (read, unread) |
| priority | string | - | Filter by priority (low, medium, high, urgent) |
| category | string | - | Filter by category |
| dateFrom | string | - | Start date filter (YYYY-MM-DD) |
| dateTo | string | - | End date filter (YYYY-MM-DD) |
| senderId | string | - | Filter by sender |

### Response

```json
{
  "success": true,
  "data": {
    "notifications": [
      {
        "id": "notif-001",
        "type": "leave",
        "category": "approval_required",
        "title": "Leave Request Requires Approval",
        "message": "John Doe has submitted a leave request for 3 days starting March 1st, 2024. Please review and approve.",
        "priority": "medium",
        "status": "unread",
        "recipients": ["mgr-001"],
        "sender": {
          "id": "emp-001",
          "firstName": "John",
          "lastName": "Doe",
          "role": "employee"
        },
        "data": {
          "leaveRequestId": "leave-001",
          "employeeId": "emp-001",
          "startDate": "2024-03-01",
          "endDate": "2024-03-03",
          "days": 3,
          "leaveType": "vacation"
        },
        "actions": [
          {
            "label": "Approve",
            "action": "approve_leave",
            "url": "/api/leave/leave-001/approve",
            "method": "POST",
            "style": "primary"
          },
          {
            "label": "View Details",
            "action": "view_leave",
            "url": "/leave/leave-001",
            "method": "GET",
            "style": "secondary"
          }
        ],
        "createdAt": "2024-02-20T14:30:00Z",
        "updatedAt": "2024-02-20T14:30:00Z",
        "readAt": null,
        "expiresAt": "2024-03-20T14:30:00Z",
        "channel": "web",
        "deliveryStatus": "delivered",
        "tags": ["leave", "approval", "urgent"]
      },
      {
        "id": "notif-002",
        "type": "announcement",
        "category": "company_update",
        "title": "New Company Policy Update",
        "message": "We've updated our remote work policy. Please review the new guidelines effective March 1st, 2024.",
        "priority": "high",
        "status": "unread",
        "recipients": ["all"],
        "sender": {
          "id": "hr-001",
          "firstName": "Jane",
          "lastName": "Smith",
          "role": "hr"
        },
        "data": {
          "policyId": "policy-001",
          "effectiveDate": "2024-03-01",
          "documentUrl": "/documents/remote-work-policy.pdf"
        },
        "actions": [
          {
            "label": "Read Policy",
            "action": "view_document",
            "url": "/documents/policy-001",
            "method": "GET",
            "style": "primary"
          },
          {
            "label": "Acknowledge",
            "action": "acknowledge_policy",
            "url": "/api/policies/policy-001/acknowledge",
            "method": "POST",
            "style": "secondary"
          }
        ],
        "createdAt": "2024-02-19T10:00:00Z",
        "updatedAt": "2024-02-19T10:00:00Z",
        "readAt": null,
        "expiresAt": null,
        "channel": "web",
        "deliveryStatus": "delivered",
        "tags": ["announcement", "policy", "important"]
      }
    ],
    "pagination": {
      "page": 1,
      "limit": 20,
      "total": 35,
      "pages": 2
    },
    "summary": {
      "totalNotifications": 35,
      "unreadCount": 12,
      "highPriorityCount": 3,
      "urgentCount": 1
    }
  }
}
```

## GET /notifications/:id

Get detailed information about a specific notification.

### Request

```http
GET /api/notifications/notif-001
Authorization: Bearer <token>
```

### Response

```json
{
  "success": true,
  "data": {
    "notification": {
      "id": "notif-001",
      "type": "expense",
      "category": "reimbursement",
      "title": "Expense Approved - Reimbursement Processed",
      "message": "Your expense claim for $450.00 (Hotel accommodation) has been approved and will be processed for reimbursement on February 25th, 2024.",
      "priority": "medium",
      "status": "unread",
      "recipients": ["emp-001"],
      "sender": {
        "id": "mgr-001",
        "firstName": "Sarah",
        "lastName": "Johnson",
        "role": "manager",
        "department": "Engineering"
      },
      "data": {
        "expenseId": "exp-001",
        "employeeId": "emp-001",
        "amount": 450.00,
        "currency": "USD",
        "reimbursementDate": "2024-02-25",
        "approvedBy": "mgr-001",
        "expenseTitle": "Hotel accommodation"
      },
      "actions": [
        {
          "label": "View Expense",
          "action": "view_expense",
          "url": "/expenses/exp-001",
          "method": "GET",
          "style": "primary",
          "icon": "eye"
        },
        {
          "label": "Download Receipt",
          "action": "download_receipt",
          "url": "/api/expenses/exp-001/receipt",
          "method": "GET",
          "style": "secondary",
          "icon": "download"
        }
      ],
      "attachments": [
        {
          "id": "att-001",
          "filename": "expense_approval.pdf",
          "url": "/api/documents/doc-att-001/download",
          "size": 125000,
          "type": "pdf"
        }
      ],
      "metadata": {
        "source": "expense_workflow",
        "workflowId": "workflow-001",
        "stepId": "step-003",
        "correlation": "exp-001-approval"
      },
      "createdAt": "2024-02-20T16:45:00Z",
      "updatedAt": "2024-02-20T16:45:00Z",
      "readAt": null,
      "expiresAt": null,
      "scheduledFor": null,
      "channels": [
        {
          "type": "web",
          "status": "delivered",
          "deliveredAt": "2024-02-20T16:45:00Z"
        },
        {
          "type": "email",
          "status": "delivered",
          "deliveredAt": "2024-02-20T16:46:00Z",
          "emailAddress": "john.doe@company.com"
        }
      ],
      "acknowledgmentRequired": false,
      "acknowledgedAt": null,
      "tags": ["expense", "approval", "reimbursement"],
      "customFields": {
        "department": "Engineering",
        "costCenter": "CC-001"
      }
    }
  }
}
```

## POST /notifications

Create a new notification (HR/Admin only).

### Request

```http
POST /api/notifications
Authorization: Bearer <token>
Content-Type: application/json

{
  "type": "announcement",
  "category": "company_update",
  "title": "Office Closure - Martin Luther King Jr. Day",
  "message": "Our offices will be closed on Monday, January 15th, 2024 in observance of Martin Luther King Jr. Day. Regular operations will resume on Tuesday, January 16th.",
  "priority": "medium",
  "recipients": ["all"],
  "data": {
    "closureDate": "2024-01-15",
    "reopenDate": "2024-01-16",
    "type": "holiday"
  },
  "channels": ["web", "email"],
  "scheduledFor": "2024-01-10T09:00:00Z",
  "expiresAt": "2024-01-16T00:00:00Z",
  "tags": ["holiday", "closure", "announcement"]
}
```

### Request Body Schema

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| type | string | Yes | Notification type |
| category | string | No | Notification category |
| title | string | Yes | Notification title |
| message | string | Yes | Notification message |
| priority | string | No | Priority level (low, medium, high, urgent) |
| recipients | array | Yes | Recipient IDs or ["all"] |
| data | object | No | Additional notification data |
| actions | array | No | Action buttons |
| channels | array | No | Delivery channels |
| scheduledFor | string | No | Schedule notification |
| expiresAt | string | No | Expiration date |
| acknowledgmentRequired | boolean | No | Requires acknowledgment |
| tags | array | No | Notification tags |

### Response

```json
{
  "success": true,
  "message": "Notification created successfully",
  "data": {
    "notification": {
      "id": "notif-003",
      "type": "announcement",
      "title": "Office Closure - Martin Luther King Jr. Day",
      "priority": "medium",
      "recipients": ["all"],
      "scheduledFor": "2024-01-10T09:00:00Z",
      "createdAt": "2024-01-08T14:00:00Z",
      "status": "scheduled"
    }
  }
}
```

## POST /notifications/:id/read

Mark a notification as read.

### Request

```http
POST /api/notifications/notif-001/read
Authorization: Bearer <token>
```

### Response

```json
{
  "success": true,
  "message": "Notification marked as read",
  "data": {
    "notification": {
      "id": "notif-001",
      "status": "read",
      "readAt": "2024-02-21T10:30:00Z"
    }
  }
}
```

## POST /notifications/bulk-read

Mark multiple notifications as read.

### Request

```http
POST /api/notifications/bulk-read
Authorization: Bearer <token>
Content-Type: application/json

{
  "notificationIds": ["notif-001", "notif-002", "notif-003"],
  "markAll": false
}
```

### Request Body Schema

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| notificationIds | array | No | Specific notification IDs to mark as read |
| markAll | boolean | No | Mark all notifications as read |

### Response

```json
{
  "success": true,
  "message": "3 notifications marked as read",
  "data": {
    "markedAsRead": 3,
    "remainingUnread": 9
  }
}
```

## GET /notifications/unread/count

Get count of unread notifications.

### Request

```http
GET /api/notifications/unread/count
Authorization: Bearer <token>
```

### Response

```json
{
  "success": true,
  "data": {
    "unreadCount": 12,
    "byPriority": {
      "urgent": 1,
      "high": 3,
      "medium": 6,
      "low": 2
    },
    "byType": {
      "leave": 4,
      "expense": 3,
      "timesheet": 2,
      "announcement": 2,
      "system": 1
    }
  }
}
```

## POST /notifications/send

Send immediate notification to users.

### Request

```http
POST /api/notifications/send
Authorization: Bearer <token>
Content-Type: application/json

{
  "type": "system",
  "category": "maintenance",
  "title": "Scheduled Maintenance Tonight",
  "message": "The HRMS system will undergo scheduled maintenance tonight from 11 PM to 1 AM PST. The system will be temporarily unavailable during this time.",
  "priority": "high",
  "recipients": ["all"],
  "channels": ["web", "email", "sms"],
  "data": {
    "maintenanceStart": "2024-02-21T23:00:00Z",
    "maintenanceEnd": "2024-02-22T01:00:00Z",
    "affectedServices": ["hrms", "payroll", "timesheets"]
  },
  "immediate": true
}
```

### Response

```json
{
  "success": true,
  "message": "Notification sent successfully",
  "data": {
    "notification": {
      "id": "notif-004",
      "sentAt": "2024-02-21T15:00:00Z",
      "recipientCount": 145,
      "deliveryStatus": {
        "web": {
          "sent": 145,
          "delivered": 145,
          "failed": 0
        },
        "email": {
          "sent": 145,
          "delivered": 142,
          "failed": 3
        },
        "sms": {
          "sent": 145,
          "delivered": 140,
          "failed": 5
        }
      }
    }
  }
}
```

## GET /notifications/templates

Get notification templates (HR/Admin only).

### Request

```http
GET /api/notifications/templates?category=leave
Authorization: Bearer <token>
```

### Response

```json
{
  "success": true,
  "data": {
    "templates": [
      {
        "id": "template-001",
        "name": "Leave Request Approval",
        "type": "leave",
        "category": "approval_required",
        "title": "Leave Request Requires Your Approval",
        "message": "{{employee_name}} has submitted a {{leave_type}} request for {{days}} days from {{start_date}} to {{end_date}}. Please review and take action.",
        "priority": "medium",
        "variables": [
          "employee_name",
          "leave_type",
          "days",
          "start_date",
          "end_date",
          "leave_request_id"
        ],
        "actions": [
          {
            "label": "Approve",
            "action": "approve_leave",
            "url": "/api/leave/{{leave_request_id}}/approve",
            "method": "POST",
            "style": "primary"
          },
          {
            "label": "View Details",
            "action": "view_leave",
            "url": "/leave/{{leave_request_id}}",
            "method": "GET",
            "style": "secondary"
          }
        ],
        "defaultChannels": ["web", "email"],
        "tags": ["leave", "approval"],
        "isActive": true,
        "createdAt": "2024-01-01T00:00:00Z",
        "updatedAt": "2024-01-15T10:00:00Z"
      },
      {
        "id": "template-002",
        "name": "Expense Approved",
        "type": "expense",
        "category": "status_update",
        "title": "Expense Approved - Reimbursement Scheduled",
        "message": "Your expense claim for {{amount}} ({{expense_title}}) has been approved and will be reimbursed on {{reimbursement_date}}.",
        "priority": "medium",
        "variables": [
          "amount",
          "currency",
          "expense_title",
          "reimbursement_date",
          "approver_name"
        ],
        "actions": [
          {
            "label": "View Expense",
            "action": "view_expense",
            "url": "/expenses/{{expense_id}}",
            "method": "GET",
            "style": "primary"
          }
        ],
        "defaultChannels": ["web", "email"],
        "tags": ["expense", "approval", "reimbursement"],
        "isActive": true,
        "createdAt": "2024-01-01T00:00:00Z",
        "updatedAt": "2024-01-10T14:00:00Z"
      }
    ]
  }
}
```

## DELETE /notifications/:id

Delete a notification (HR/Admin only).

### Request

```http
DELETE /api/notifications/notif-001
Authorization: Bearer <token>
```

### Response

```json
{
  "success": true,
  "message": "Notification deleted successfully"
}
```

## GET /notifications/stats/overview

Get notification statistics and analytics.

### Request

```http
GET /api/notifications/stats/overview?period=monthly&year=2024&month=2
Authorization: Bearer <token>
```

### Response

```json
{
  "success": true,
  "data": {
    "stats": {
      "overview": {
        "totalNotifications": 1250,
        "totalSent": 1180,
        "totalRead": 945,
        "totalUnread": 235,
        "readRate": 80.1,
        "averageReadTime": 2.5,
        "clickThroughRate": 35.2
      },
      "byType": [
        {
          "type": "leave",
          "count": 350,
          "readRate": 92.3,
          "avgReadTime": 1.8
        },
        {
          "type": "expense",
          "count": 280,
          "readRate": 87.5,
          "avgReadTime": 2.1
        },
        {
          "type": "announcement",
          "count": 120,
          "readRate": 65.0,
          "avgReadTime": 4.2
        },
        {
          "type": "system",
          "count": 45,
          "readRate": 98.0,
          "avgReadTime": 0.8
        }
      ],
      "byPriority": {
        "urgent": {
          "count": 25,
          "readRate": 98.0,
          "avgReadTime": 0.5
        },
        "high": {
          "count": 185,
          "readRate": 92.4,
          "avgReadTime": 1.2
        },
        "medium": {
          "count": 650,
          "readRate": 78.5,
          "avgReadTime": 2.8
        },
        "low": {
          "count": 390,
          "readRate": 65.2,
          "avgReadTime": 4.5
        }
      },
      "deliveryStats": {
        "web": {
          "sent": 1180,
          "delivered": 1180,
          "deliveryRate": 100.0
        },
        "email": {
          "sent": 980,
          "delivered": 955,
          "deliveryRate": 97.4,
          "bounced": 15,
          "opened": 720,
          "openRate": 75.4
        },
        "sms": {
          "sent": 125,
          "delivered": 118,
          "deliveryRate": 94.4,
          "failed": 7
        }
      },
      "engagementTrends": [
        {
          "date": "2024-02-01",
          "sent": 45,
          "read": 38,
          "clicked": 12
        },
        {
          "date": "2024-02-02",
          "sent": 52,
          "read": 43,
          "clicked": 18
        }
      ],
      "topPerformingNotifications": [
        {
          "id": "notif-template-001",
          "title": "Leave Request Approval",
          "type": "leave",
          "sent": 250,
          "readRate": 95.2,
          "clickRate": 78.4
        }
      ],
      "unreadByAge": {
        "last24Hours": 45,
        "last7Days": 125,
        "last30Days": 235,
        "older": 0
      }
    }
  }
}
```

## Notification Types and Categories

### Types

| Type | Description | Default Priority |
|------|-------------|------------------|
| leave | Leave requests and approvals | medium |
| expense | Expense reports and reimbursements | medium |
| timesheet | Timesheet submissions and approvals | medium |
| payroll | Payroll processing notifications | high |
| system | System maintenance and updates | high |
| announcement | Company announcements | medium |
| reminder | Deadline and task reminders | low |
| alert | Important alerts and warnings | high |

### Categories

| Category | Description | Examples |
|----------|-------------|----------|
| approval_required | Requires manager approval | Leave requests, expense claims |
| status_update | Status changes | Approved requests, processed payments |
| reminder | Deadline reminders | Timesheet deadlines, document expiry |
| announcement | Company communications | Policy updates, events |
| system | System notifications | Maintenance, outages |
| birthday | Employee birthdays | Birthday reminders |
| work_anniversary | Work anniversaries | Service milestone celebrations |

## Notification Channels

| Channel | Description | Real-time | Delivery Time |
|---------|-------------|-----------|---------------|
| web | In-app notifications | Yes | Immediate |
| email | Email notifications | No | 1-5 minutes |
| sms | SMS notifications | No | 1-2 minutes |
| push | Mobile push notifications | Yes | Immediate |
| slack | Slack integration | Yes | Immediate |

## Priority Levels

| Priority | Description | Auto-read After | Channels |
|----------|-------------|------------------|----------|
| urgent | Critical actions required | Never | All enabled |
| high | Important but not critical | 7 days | web, email, sms |
| medium | Standard notifications | 14 days | web, email |
| low | Informational | 30 days | web |

## Error Responses

### Notification Not Found (404)

```json
{
  "success": false,
  "message": "Notification not found or access denied"
}
```

### Invalid Recipients (400)

```json
{
  "success": false,
  "message": "Invalid recipient list. Please specify valid user IDs or 'all'"
}
```

### Template Not Found (404)

```json
{
  "success": false,
  "message": "Notification template not found"
}
```

## Usage Examples

### JavaScript Notification Management

```javascript
// Get unread notifications
const getUnreadNotifications = async () => {
  const response = await fetch('/api/notifications?status=unread&limit=50', {
    headers: {
      'Authorization': `Bearer ${token}`
    }
  });
  
  return response.json();
};

// Mark notification as read
const markAsRead = async (notificationId) => {
  const response = await fetch(`/api/notifications/${notificationId}/read`, {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${token}`
    }
  });
  
  return response.json();
};

// Send announcement
const sendAnnouncement = async (title, message, priority = 'medium') => {
  const response = await fetch('/api/notifications/send', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${token}`,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      type: 'announcement',
      category: 'company_update',
      title,
      message,
      priority,
      recipients: ['all'],
      channels: ['web', 'email'],
      immediate: true
    })
  });
  
  return response.json();
};

// Get notification count
const getNotificationCount = async () => {
  const response = await fetch('/api/notifications/unread/count', {
    headers: {
      'Authorization': `Bearer ${token}`
    }
  });
  
  return response.json();
};
```

### React Notification Center

```javascript
import { useState, useEffect } from 'react';

const NotificationCenter = () => {
  const [notifications, setNotifications] = useState([]);
  const [unreadCount, setUnreadCount] = useState(0);
  const [loading, setLoading] = useState(false);

  useEffect(() => {
    loadNotifications();
    loadUnreadCount();
    
    // Poll for new notifications every 30 seconds
    const interval = setInterval(() => {
      loadUnreadCount();
    }, 30000);
    
    return () => clearInterval(interval);
  }, []);

  const loadNotifications = async () => {
    setLoading(true);
    try {
      const response = await fetch('/api/notifications?limit=20', {
        headers: {
          'Authorization': `Bearer ${localStorage.getItem('token')}`
        }
      });
      
      const result = await response.json();
      
      if (result.success) {
        setNotifications(result.data.notifications);
      }
    } catch (error) {
      console.error('Error loading notifications:', error);
    } finally {
      setLoading(false);
    }
  };

  const loadUnreadCount = async () => {
    try {
      const response = await fetch('/api/notifications/unread/count', {
        headers: {
          'Authorization': `Bearer ${localStorage.getItem('token')}`
        }
      });
      
      const result = await response.json();
      
      if (result.success) {
        setUnreadCount(result.data.unreadCount);
      }
    } catch (error) {
      console.error('Error loading unread count:', error);
    }
  };

  const markAsRead = async (notificationId) => {
    try {
      const response = await fetch(`/api/notifications/${notificationId}/read`, {
        method: 'POST',
        headers: {
          'Authorization': `Bearer ${localStorage.getItem('token')}`
        }
      });

      if (response.ok) {
        setNotifications(notifications.map(n => 
          n.id === notificationId ? { ...n, status: 'read', readAt: new Date().toISOString() } : n
        ));
        setUnreadCount(Math.max(0, unreadCount - 1));
      }
    } catch (error) {
      console.error('Error marking as read:', error);
    }
  };

  const markAllAsRead = async () => {
    try {
      const response = await fetch('/api/notifications/bulk-read', {
        method: 'POST',
        headers: {
          'Authorization': `Bearer ${localStorage.getItem('token')}`,
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({ markAll: true })
      });

      if (response.ok) {
        setNotifications(notifications.map(n => ({ 
          ...n, 
          status: 'read', 
          readAt: new Date().toISOString() 
        })));
        setUnreadCount(0);
      }
    } catch (error) {
      console.error('Error marking all as read:', error);
    }
  };

  const getPriorityColor = (priority) => {
    const colors = {
      urgent: 'text-red-600 bg-red-50 border-red-200',
      high: 'text-orange-600 bg-orange-50 border-orange-200',
      medium: 'text-blue-600 bg-blue-50 border-blue-200',
      low: 'text-gray-600 bg-gray-50 border-gray-200'
    };
    return colors[priority] || colors.medium;
  };

  return (
    <div className="notification-center">
      <div className="header flex justify-between items-center">
        <h2 className="text-xl font-semibold">
          Notifications {unreadCount > 0 && (
            <span className="badge bg-red-500 text-white ml-2">
              {unreadCount}
            </span>
          )}
        </h2>
        
        {unreadCount > 0 && (
          <button 
            onClick={markAllAsRead}
            className="text-sm text-blue-600 hover:text-blue-800"
          >
            Mark all as read
          </button>
        )}
      </div>

      <div className="notifications-list">
        {loading ? (
          <div className="loading">Loading notifications...</div>
        ) : notifications.length === 0 ? (
          <div className="no-notifications">No notifications</div>
        ) : (
          notifications.map(notification => (
            <div
              key={notification.id}
              className={`notification-item p-4 border-l-4 mb-3 cursor-pointer
                ${notification.status === 'unread' ? 'bg-blue-50 border-l-blue-400' : 'bg-white border-l-gray-200'}
                ${getPriorityColor(notification.priority)}
              `}
              onClick={() => notification.status === 'unread' && markAsRead(notification.id)}
            >
              <div className="flex items-start justify-between">
                <div className="flex-1">
                  <div className="flex items-center">
                    <h3 className="font-medium text-gray-900">
                      {notification.title}
                    </h3>
                    <span className={`ml-2 px-2 py-1 text-xs rounded ${getPriorityColor(notification.priority)}`}>
                      {notification.priority}
                    </span>
                  </div>
                  
                  <p className="text-gray-700 mt-1">
                    {notification.message}
                  </p>
                  
                  <div className="flex items-center mt-2 text-sm text-gray-500">
                    <span>{formatDate(notification.createdAt)}</span>
                    {notification.sender && (
                      <span className="ml-4">
                        From: {notification.sender.firstName} {notification.sender.lastName}
                      </span>
                    )}
                  </div>

                  {notification.actions && notification.actions.length > 0 && (
                    <div className="actions mt-3 flex space-x-2">
                      {notification.actions.map((action, index) => (
                        <button
                          key={index}
                          className={`px-3 py-1 text-sm rounded
                            ${action.style === 'primary' 
                              ? 'bg-blue-600 text-white hover:bg-blue-700' 
                              : 'bg-gray-200 text-gray-700 hover:bg-gray-300'
                            }`}
                          onClick={(e) => {
                            e.stopPropagation();
                            handleAction(action);
                          }}
                        >
                          {action.label}
                        </button>
                      ))}
                    </div>
                  )}
                </div>

                {notification.status === 'unread' && (
                  <div className="w-3 h-3 bg-blue-500 rounded-full ml-3 mt-1"></div>
                )}
              </div>
            </div>
          ))
        )}
      </div>
    </div>
  );
};

const formatDate = (dateString) => {
  const date = new Date(dateString);
  const now = new Date();
  const diffMs = now - date;
  const diffHours = Math.floor(diffMs / (1000 * 60 * 60));
  const diffDays = Math.floor(diffHours / 24);

  if (diffHours < 1) {
    return 'Just now';
  } else if (diffHours < 24) {
    return `${diffHours} hour${diffHours > 1 ? 's' : ''} ago`;
  } else if (diffDays < 7) {
    return `${diffDays} day${diffDays > 1 ? 's' : ''} ago`;
  } else {
    return date.toLocaleDateString();
  }
};

const handleAction = (action) => {
  if (action.method === 'GET') {
    // Navigate to URL
    window.location.href = action.url;
  } else {
    // Make API call
    fetch(action.url, {
      method: action.method,
      headers: {
        'Authorization': `Bearer ${localStorage.getItem('token')}`,
        'Content-Type': 'application/json'
      }
    }).then(response => {
      if (response.ok) {
        console.log('Action completed successfully');
        // Refresh notifications or update UI as needed
      }
    });
  }
};
```
