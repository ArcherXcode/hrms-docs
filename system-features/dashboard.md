# 📊 Dashboard API

The Dashboard API provides comprehensive analytics, metrics, and real-time data for administrative and employee dashboards with role-based views and interactive widgets.

## Base Endpoint

```
https://api.upsurgemedia.in/api/v1/dashboard
```

## Overview

The Dashboard API delivers personalized dashboard data based on user roles, providing key metrics, charts, notifications, and actionable insights for HR management, attendance tracking, and organizational analytics.

## Dashboard Models

```typescript
interface DashboardData {
  id: string;                      // Dashboard UUID
  userId: string;                  // User ID
  userRole: UserRole;              // User role
  companyId: string;               // Company ID
  
  // Widgets and Layout
  widgets: DashboardWidget[];      // Dashboard widgets
  layout: LayoutConfig[];          // Widget layout configuration
  preferences: DashboardPreferences; // User preferences
  
  // Data and Metrics
  metrics: DashboardMetrics;       // Key metrics
  charts: ChartData[];             // Chart data
  notifications: Notification[];   // Recent notifications
  quickActions: QuickAction[];     // Available actions
  
  // Timestamps
  lastRefreshed: Date;             // Last data refresh
  lastAccessedAt: Date;            // Last dashboard access
  
  // Relationships
  user?: User;                     // Dashboard owner
  company?: Company;               // Associated company
}

interface DashboardWidget {
  id: string;                      // Widget UUID
  type: WidgetType;                // Widget type
  title: string;                   // Widget title
  position: WidgetPosition;        // Widget position
  size: WidgetSize;                // Widget size
  
  // Data and Configuration
  data: any;                       // Widget data
  config: WidgetConfig;            // Widget configuration
  refreshInterval?: number;        // Auto-refresh interval (minutes)
  
  // Permissions and Visibility
  isVisible: boolean;              // Widget visibility
  requiresPermission?: string[];   // Required permissions
  
  // Timestamps
  lastUpdated: Date;               // Last data update
  createdAt: Date;
  updatedAt: Date;
}

interface DashboardMetrics {
  // Employee Metrics
  totalEmployees: number;          // Total employee count
  activeEmployees: number;         // Active employees
  newHiresThisMonth: number;       // New hires count
  employeeTurnover: number;        // Turnover rate
  
  // Attendance Metrics
  presentToday: number;            // Present employees today
  absentToday: number;             // Absent employees today
  avgAttendanceRate: number;       // Average attendance rate
  lateArrivals: number;            // Late arrivals today
  
  // Leave Metrics
  pendingLeaveRequests: number;    // Pending leave requests
  employeesOnLeave: number;        // Employees on leave today
  leaveApprovalRate: number;       // Leave approval rate
  
  // Department Metrics
  totalDepartments: number;        // Total departments
  avgDepartmentSize: number;       // Average department size
  
  // Financial Metrics
  monthlyPayroll: number;          // Monthly payroll amount
  pendingExpenses: number;         // Pending expense claims
  budgetUtilization: number;       // Budget utilization %
  
  // Performance Metrics
  performanceReviews: {
    completed: number;             // Completed reviews
    pending: number;               // Pending reviews
    overdue: number;               // Overdue reviews
  };
  
  // Time Tracking
  totalHoursWorked: number;        // Total hours this month
  overtimeHours: number;           // Overtime hours
  avgHoursPerEmployee: number;     // Average hours per employee
}

interface ChartData {
  id: string;                      // Chart UUID
  type: ChartType;                 // Chart type
  title: string;                   // Chart title
  data: ChartDataPoint[];          // Chart data points
  config: ChartConfig;             // Chart configuration
  period: TimePeriod;              // Time period
  lastUpdated: Date;               // Last update timestamp
}

interface QuickAction {
  id: string;                      // Action UUID
  type: ActionType;                // Action type
  title: string;                   // Action title
  description: string;             // Action description
  icon: string;                    // Action icon
  url: string;                     // Action URL
  requiresPermission?: string[];   // Required permissions
  isHighPriority: boolean;         // High priority flag
  badge?: {                        // Optional badge
    count: number;
    color: string;
  };
}

enum WidgetType {
  METRIC_CARD = 'metric_card',
  LINE_CHART = 'line_chart',
  BAR_CHART = 'bar_chart',
  PIE_CHART = 'pie_chart',
  TABLE = 'table',
  CALENDAR = 'calendar',
  PROGRESS_BAR = 'progress_bar',
  NOTIFICATION_FEED = 'notification_feed',
  QUICK_ACTIONS = 'quick_actions',
  EMPLOYEE_LIST = 'employee_list',
  ATTENDANCE_HEATMAP = 'attendance_heatmap',
  LEAVE_CALENDAR = 'leave_calendar'
}

enum ChartType {
  LINE = 'line',
  BAR = 'bar',
  PIE = 'pie',
  DOUGHNUT = 'doughnut',
  AREA = 'area',
  SCATTER = 'scatter',
  RADAR = 'radar'
}

enum TimePeriod {
  TODAY = 'today',
  WEEK = 'week',
  MONTH = 'month',
  QUARTER = 'quarter',
  YEAR = 'year',
  CUSTOM = 'custom'
}

enum ActionType {
  CREATE_EMPLOYEE = 'create_employee',
  APPROVE_LEAVE = 'approve_leave',
  PROCESS_PAYROLL = 'process_payroll',
  GENERATE_REPORT = 'generate_report',
  REVIEW_ATTENDANCE = 'review_attendance',
  UPDATE_PROFILE = 'update_profile',
  VIEW_NOTIFICATIONS = 'view_notifications'
}
```

## Dashboard Endpoints

### Get Dashboard Data

Retrieve complete dashboard data for the authenticated user.

**Endpoint:** `GET /dashboard`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `refresh` (boolean): Force refresh data from database
- `widgets` (string[]): Specific widgets to load (comma-separated)
- `period` (string): Time period for metrics (today, week, month, quarter, year)

**Response:**
```json
{
  "data": {
    "id": "dashboard-uuid",
    "userId": "user-uuid",
    "userRole": "admin",
    "companyId": "company-uuid",
    "metrics": {
      "totalEmployees": 250,
      "activeEmployees": 245,
      "newHiresThisMonth": 8,
      "employeeTurnover": 12.5,
      "presentToday": 185,
      "absentToday": 60,
      "avgAttendanceRate": 87.5,
      "lateArrivals": 12,
      "pendingLeaveRequests": 15,
      "employeesOnLeave": 22,
      "leaveApprovalRate": 92.3,
      "totalDepartments": 8,
      "avgDepartmentSize": 31,
      "monthlyPayroll": 485000,
      "pendingExpenses": 12500,
      "budgetUtilization": 78.5,
      "performanceReviews": {
        "completed": 180,
        "pending": 45,
        "overdue": 8
      },
      "totalHoursWorked": 9240,
      "overtimeHours": 340,
      "avgHoursPerEmployee": 37.8
    },
    "widgets": [
      {
        "id": "widget-1",
        "type": "metric_card",
        "title": "Total Employees",
        "position": { "x": 0, "y": 0 },
        "size": { "width": 3, "height": 2 },
        "data": {
          "value": 250,
          "change": "+8",
          "changePercentage": 3.3,
          "trend": "up",
          "icon": "users"
        },
        "isVisible": true,
        "lastUpdated": "2025-09-03T10:00:00Z"
      },
      {
        "id": "widget-2",
        "type": "line_chart",
        "title": "Attendance Trend",
        "position": { "x": 3, "y": 0 },
        "size": { "width": 6, "height": 4 },
        "data": {
          "labels": ["Mon", "Tue", "Wed", "Thu", "Fri"],
          "datasets": [
            {
              "label": "Present",
              "data": [180, 185, 175, 190, 185],
              "borderColor": "#10B981",
              "backgroundColor": "rgba(16, 185, 129, 0.1)"
            },
            {
              "label": "Absent",
              "data": [70, 65, 75, 60, 65],
              "borderColor": "#EF4444",
              "backgroundColor": "rgba(239, 68, 68, 0.1)"
            }
          ]
        },
        "isVisible": true,
        "lastUpdated": "2025-09-03T10:00:00Z"
      }
    ],
    "charts": [
      {
        "id": "chart-1",
        "type": "pie",
        "title": "Department Distribution",
        "data": [
          { "label": "Engineering", "value": 85, "color": "#3B82F6" },
          { "label": "Sales", "value": 45, "color": "#10B981" },
          { "label": "Marketing", "value": 35, "color": "#F59E0B" },
          { "label": "HR", "value": 25, "color": "#EF4444" },
          { "label": "Finance", "value": 20, "color": "#8B5CF6" },
          { "label": "Operations", "value": 40, "color": "#06B6D4" }
        ],
        "period": "month",
        "lastUpdated": "2025-09-03T10:00:00Z"
      }
    ],
    "notifications": [
      {
        "id": "notif-1",
        "type": "leave_request",
        "title": "New Leave Request",
        "message": "John Doe has requested leave from Sep 10-12",
        "priority": "medium",
        "isRead": false,
        "createdAt": "2025-09-03T09:30:00Z",
        "actionUrl": "/leave/requests/pending"
      },
      {
        "id": "notif-2",
        "type": "attendance_alert",
        "title": "Late Arrival Alert",
        "message": "12 employees arrived late today",
        "priority": "low",
        "isRead": false,
        "createdAt": "2025-09-03T09:00:00Z",
        "actionUrl": "/attendance/late-arrivals"
      }
    ],
    "quickActions": [
      {
        "id": "action-1",
        "type": "approve_leave",
        "title": "Approve Leave Requests",
        "description": "Review and approve pending leave requests",
        "icon": "calendar-check",
        "url": "/leave/pending-approval",
        "isHighPriority": true,
        "badge": {
          "count": 15,
          "color": "red"
        }
      },
      {
        "id": "action-2",
        "type": "generate_report",
        "title": "Generate Monthly Report",
        "description": "Generate attendance and payroll reports",
        "icon": "document-report",
        "url": "/reports/generate",
        "isHighPriority": false
      }
    ],
    "lastRefreshed": "2025-09-03T10:00:00Z",
    "lastAccessedAt": "2025-09-03T10:05:00Z"
  }
}
```

**Example:**
```bash
curl -X GET "https://api.upsurgemedia.in/api/v1/dashboard?period=month&refresh=true" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

### Get Specific Widget Data

Get data for a specific dashboard widget.

**Endpoint:** `GET /dashboard/widgets/{widgetId}`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `period` (string): Time period for widget data
- `refresh` (boolean): Force refresh widget data

**Response:**
```json
{
  "data": {
    "id": "widget-attendance-trend",
    "type": "line_chart",
    "title": "Attendance Trend",
    "data": {
      "labels": ["Week 1", "Week 2", "Week 3", "Week 4"],
      "datasets": [
        {
          "label": "Attendance Rate",
          "data": [85.2, 87.8, 86.5, 89.1],
          "borderColor": "#10B981",
          "backgroundColor": "rgba(16, 185, 129, 0.1)",
          "tension": 0.4
        },
        {
          "label": "Target",
          "data": [85, 85, 85, 85],
          "borderColor": "#6B7280",
          "backgroundColor": "transparent",
          "borderDash": [5, 5]
        }
      ]
    },
    "config": {
      "responsive": true,
      "plugins": {
        "legend": {
          "position": "top"
        },
        "title": {
          "display": true,
          "text": "Monthly Attendance Trend"
        }
      },
      "scales": {
        "y": {
          "beginAtZero": true,
          "max": 100,
          "ticks": {
            "callback": "value + '%'"
          }
        }
      }
    },
    "period": "month",
    "lastUpdated": "2025-09-03T10:00:00Z"
  }
}
```

### Get Employee Dashboard

Get dashboard data specific to employee role.

**Endpoint:** `GET /dashboard/employee`

**Headers:** `Authorization: Bearer <token>`

**Response:**
```json
{
  "data": {
    "personalMetrics": {
      "attendanceRate": 92.5,
      "hoursWorked": 168,
      "overtimeHours": 12,
      "leaveBalance": 18,
      "punctualityScore": 85,
      "lastCheckIn": "2025-09-03T09:15:00Z",
      "todayStatus": "present"
    },
    "recentActivity": [
      {
        "type": "check_in",
        "timestamp": "2025-09-03T09:15:00Z",
        "location": "Main Office"
      },
      {
        "type": "leave_approved",
        "timestamp": "2025-09-02T14:30:00Z",
        "details": "Annual leave approved for Sep 15-16"
      }
    ],
    "upcomingEvents": [
      {
        "type": "meeting",
        "title": "Team Standup",
        "datetime": "2025-09-03T10:30:00Z",
        "location": "Conference Room A"
      },
      {
        "type": "leave",
        "title": "Approved Leave",
        "datetime": "2025-09-15T00:00:00Z",
        "duration": "2 days"
      }
    ],
    "quickActions": [
      {
        "type": "check_out",
        "title": "Check Out",
        "enabled": true,
        "url": "/attendance/check-out"
      },
      {
        "type": "request_leave",
        "title": "Request Leave",
        "enabled": true,
        "url": "/leave/request"
      },
      {
        "type": "view_payslip",
        "title": "View Payslip",
        "enabled": true,
        "url": "/payroll/payslip"
      }
    ],
    "charts": [
      {
        "type": "attendance_calendar",
        "title": "Attendance Calendar",
        "data": {
          "month": "2025-09",
          "attendance": {
            "2025-09-01": "present",
            "2025-09-02": "present",
            "2025-09-03": "present",
            "2025-09-04": "absent",
            "2025-09-05": "late"
          }
        }
      }
    ]
  }
}
```

### Get Department Dashboard

Get dashboard data for department managers.

**Endpoint:** `GET /dashboard/department/{departmentId}`

**Headers:** `Authorization: Bearer <token>`

**Response:**
```json
{
  "data": {
    "departmentInfo": {
      "id": "dept-uuid",
      "name": "Engineering",
      "totalEmployees": 85,
      "activeEmployees": 82,
      "managerName": "Sarah Johnson"
    },
    "metrics": {
      "attendanceRate": 89.2,
      "avgHoursWorked": 41.5,
      "productivityScore": 87,
      "teamMorale": 4.2,
      "turnoverRate": 8.5
    },
    "teamPerformance": {
      "totalProjects": 12,
      "completedProjects": 8,
      "onTimeDelivery": 75,
      "bugReports": 23,
      "codeReviews": 156
    },
    "upcomingDeadlines": [
      {
        "project": "Mobile App Release",
        "deadline": "2025-09-15",
        "status": "on_track",
        "assignedTo": 15
      }
    ],
    "teamMembers": [
      {
        "employeeId": "EMP001",
        "name": "John Doe",
        "role": "Senior Developer",
        "status": "present",
        "currentTask": "API Development",
        "productivity": 92
      }
    ]
  }
}
```

## Analytics Endpoints

### Get Attendance Analytics

Get detailed attendance analytics and trends.

**Endpoint:** `GET /dashboard/analytics/attendance`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `period` (string): Analysis period (week, month, quarter, year)
- `departmentId` (string): Filter by department
- `compareWith` (string): Compare with previous period

**Response:**
```json
{
  "data": {
    "overview": {
      "totalWorkingDays": 22,
      "avgAttendanceRate": 87.5,
      "totalPresentDays": 4235,
      "totalAbsentDays": 615,
      "lateArrivals": 156,
      "earlyDepartures": 89
    },
    "trends": {
      "attendanceByDay": [
        {
          "date": "2025-09-01",
          "present": 185,
          "absent": 65,
          "rate": 74.0
        }
      ],
      "weeklyPattern": {
        "monday": 89.2,
        "tuesday": 91.5,
        "wednesday": 88.7,
        "thursday": 87.3,
        "friday": 82.1
      },
      "monthlyComparison": {
        "currentMonth": 87.5,
        "previousMonth": 85.2,
        "change": 2.3,
        "trend": "improving"
      }
    },
    "departmentBreakdown": [
      {
        "departmentName": "Engineering",
        "attendanceRate": 91.2,
        "totalEmployees": 85,
        "avgHours": 42.3
      }
    ],
    "alerts": [
      {
        "type": "low_attendance",
        "department": "Sales",
        "message": "Sales department attendance below 80% threshold",
        "severity": "medium"
      }
    ]
  }
}
```

### Get Leave Analytics

Get leave management analytics and patterns.

**Endpoint:** `GET /dashboard/analytics/leave`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `period` (string): Analysis period
- `leaveType` (string): Filter by leave type
- `departmentId` (string): Filter by department

**Response:**
```json
{
  "data": {
    "overview": {
      "totalLeaveRequests": 156,
      "approvedRequests": 142,
      "pendingRequests": 8,
      "rejectedRequests": 6,
      "approvalRate": 91.0,
      "avgProcessingTime": 1.8
    },
    "leaveTypes": [
      {
        "type": "annual",
        "requests": 89,
        "daysUsed": 445,
        "avgDaysPerEmployee": 5.2
      },
      {
        "type": "sick",
        "requests": 45,
        "daysUsed": 67,
        "avgDaysPerEmployee": 1.5
      }
    ],
    "seasonalTrends": [
      {
        "month": "January",
        "requests": 23,
        "pattern": "low"
      }
    ],
    "upcomingLeaves": [
      {
        "startDate": "2025-09-15",
        "employeesOnLeave": 12,
        "departments": ["Engineering", "Marketing"],
        "impact": "medium"
      }
    ]
  }
}
```

### Get Payroll Analytics

Get payroll and compensation analytics.

**Endpoint:** `GET /dashboard/analytics/payroll`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `period` (string): Analysis period
- `departmentId` (string): Filter by department
- `includeProjections` (boolean): Include future projections

**Response:**
```json
{
  "data": {
    "overview": {
      "totalPayroll": 485000,
      "avgSalary": 58200,
      "totalOvertimePay": 45600,
      "totalBenefits": 89500,
      "payrollGrowth": 8.5
    },
    "departmentCosts": [
      {
        "department": "Engineering",
        "totalCost": 185000,
        "avgSalary": 75000,
        "employees": 85,
        "percentageOfTotal": 38.1
      }
    ],
    "salaryDistribution": [
      {
        "range": "40000-60000",
        "count": 125,
        "percentage": 50.0
      },
      {
        "range": "60000-80000",
        "count": 75,
        "percentage": 30.0
      }
    ],
    "trends": {
      "monthlyPayroll": [
        {
          "month": "2025-01",
          "amount": 465000,
          "growth": 3.2
        }
      ],
      "overtimeTrends": [
        {
          "month": "2025-01",
          "hours": 340,
          "cost": 8500
        }
      ]
    },
    "projections": {
      "nextQuarterPayroll": 1485000,
      "annualProjection": 5940000,
      "budgetVariance": -2.1
    }
  }
}
```

## Widget Management

### Update Widget Configuration

Update widget settings and preferences.

**Endpoint:** `PATCH /dashboard/widgets/{widgetId}`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "position": { "x": 0, "y": 0 },
  "size": { "width": 6, "height": 4 },
  "isVisible": true,
  "refreshInterval": 15,
  "config": {
    "showLegend": true,
    "chartType": "line",
    "timeRange": "month"
  }
}
```

**Response:**
```json
{
  "data": {
    "widgetId": "widget-uuid",
    "position": { "x": 0, "y": 0 },
    "size": { "width": 6, "height": 4 },
    "isVisible": true,
    "refreshInterval": 15,
    "updatedAt": "2025-09-03T11:00:00Z"
  },
  "message": "Widget configuration updated successfully"
}
```

### Reset Dashboard Layout

Reset dashboard to default layout.

**Endpoint:** `POST /dashboard/reset`

**Headers:** `Authorization: Bearer <token>`

**Response:**
```json
{
  "data": {
    "dashboardId": "dashboard-uuid",
    "layout": "default",
    "widgets": 8,
    "resetAt": "2025-09-03T11:30:00Z"
  },
  "message": "Dashboard layout reset to default"
}
```

## Real-time Updates

### Get Dashboard Updates

Get real-time dashboard updates via WebSocket or Server-Sent Events.

**Endpoint:** `GET /dashboard/updates`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `type` (string): Update type (metrics, notifications, alerts)
- `widgets` (string[]): Specific widgets to monitor

**WebSocket Connection:**
```javascript
const ws = new WebSocket('wss://api.upsurgemedia.in/api/v1/dashboard/updates?token=YOUR_TOKEN');

ws.onmessage = function(event) {
  const update = JSON.parse(event.data);
  
  switch(update.type) {
    case 'metric_update':
      updateMetricWidget(update.widgetId, update.data);
      break;
    case 'notification':
      addNotification(update.notification);
      break;
    case 'alert':
      showAlert(update.alert);
      break;
  }
};
```

## Integration Examples

### JavaScript/React Dashboard

```javascript
class DashboardService {
  constructor(baseURL = 'https://api.upsurgemedia.in') {
    this.api = axios.create({ baseURL });
    this.ws = null;
  }

  setToken(token) {
    this.api.defaults.headers.common['Authorization'] = `Bearer ${token}`;
    this.token = token;
  }

  async getDashboard(params = {}) {
    const response = await this.api.get('/dashboard', { params });
    return response.data.data;
  }

  async getEmployeeDashboard() {
    const response = await this.api.get('/dashboard/employee');
    return response.data.data;
  }

  async getDepartmentDashboard(departmentId) {
    const response = await this.api.get(`/dashboard/department/${departmentId}`);
    return response.data.data;
  }

  async getAnalytics(type, params = {}) {
    const response = await this.api.get(`/dashboard/analytics/${type}`, { params });
    return response.data.data;
  }

  async updateWidget(widgetId, config) {
    const response = await this.api.patch(`/dashboard/widgets/${widgetId}`, config);
    return response.data.data;
  }

  connectRealtime(onUpdate) {
    const wsUrl = `wss://api.upsurgemedia.in/api/v1/dashboard/updates?token=${this.token}`;
    this.ws = new WebSocket(wsUrl);
    
    this.ws.onmessage = (event) => {
      const update = JSON.parse(event.data);
      onUpdate(update);
    };
    
    this.ws.onerror = (error) => {
      console.error('WebSocket error:', error);
    };
    
    return this.ws;
  }

  disconnectRealtime() {
    if (this.ws) {
      this.ws.close();
      this.ws = null;
    }
  }
}

// Usage
const dashboardService = new DashboardService();
dashboardService.setToken('your-jwt-token');

// Load dashboard
const dashboard = await dashboardService.getDashboard({
  period: 'month',
  refresh: true
});

// Get attendance analytics
const attendanceAnalytics = await dashboardService.getAnalytics('attendance', {
  period: 'month',
  departmentId: 'dept-uuid'
});

// Connect to real-time updates
dashboardService.connectRealtime((update) => {
  console.log('Dashboard update:', update);
  
  switch(update.type) {
    case 'metric_update':
      // Update specific metric widget
      updateMetricDisplay(update.widgetId, update.data);
      break;
    case 'notification':
      // Show new notification
      showNotification(update.notification);
      break;
  }
});
```

### Python Dashboard Analytics

```python
import requests
import json
from typing import Dict, List, Optional
from datetime import datetime, timedelta

class DashboardAPI:
    def __init__(self, base_url: str = "https://api.upsurgemedia.in"):
        self.base_url = base_url
        self.session = requests.Session()

    def set_token(self, token: str):
        self.session.headers.update({"Authorization": f"Bearer {token}"})

    def get_dashboard(self, **params) -> Dict:
        response = self.session.get(f"{self.base_url}/dashboard", params=params)
        response.raise_for_status()
        return response.json()["data"]

    def get_analytics(self, analytics_type: str, **params) -> Dict:
        response = self.session.get(
            f"{self.base_url}/dashboard/analytics/{analytics_type}",
            params=params
        )
        response.raise_for_status()
        return response.json()["data"]

    def get_employee_dashboard(self) -> Dict:
        response = self.session.get(f"{self.base_url}/dashboard/employee")
        response.raise_for_status()
        return response.json()["data"]

    def generate_attendance_report(self, period: str = "month") -> Dict:
        analytics = self.get_analytics("attendance", period=period)
        
        report = {
            "summary": {
                "period": period,
                "total_working_days": analytics["overview"]["totalWorkingDays"],
                "average_attendance": analytics["overview"]["avgAttendanceRate"],
                "late_arrivals": analytics["overview"]["lateArrivals"]
            },
            "department_performance": [],
            "trends": analytics["trends"],
            "recommendations": []
        }
        
        # Add department performance
        for dept in analytics["departmentBreakdown"]:
            report["department_performance"].append({
                "department": dept["departmentName"],
                "attendance_rate": dept["attendanceRate"],
                "employees": dept["totalEmployees"],
                "status": "good" if dept["attendanceRate"] >= 85 else "needs_attention"
            })
        
        # Generate recommendations
        if analytics["overview"]["avgAttendanceRate"] < 85:
            report["recommendations"].append("Overall attendance below target - consider attendance policy review")
        
        if analytics["overview"]["lateArrivals"] > 100:
            report["recommendations"].append("High number of late arrivals - review flexible working hours")
        
        return report

    def calculate_productivity_metrics(self, department_id: str = None) -> Dict:
        # Get various analytics
        attendance = self.get_analytics("attendance", departmentId=department_id)
        leave = self.get_analytics("leave", departmentId=department_id)
        
        productivity_score = 0
        factors = []
        
        # Attendance factor (40% weight)
        attendance_rate = attendance["overview"]["avgAttendanceRate"]
        attendance_factor = min(attendance_rate / 90 * 40, 40)
        productivity_score += attendance_factor
        factors.append(f"Attendance: {attendance_rate:.1f}% (Weight: 40%)")
        
        # Leave utilization factor (20% weight)
        leave_approval_rate = leave["overview"]["approvalRate"]
        leave_factor = leave_approval_rate / 100 * 20
        productivity_score += leave_factor
        factors.append(f"Leave Management: {leave_approval_rate:.1f}% (Weight: 20%)")
        
        # Overtime factor (20% weight) - moderate overtime is good, too much is bad
        # This would need payroll analytics
        
        # Punctuality factor (20% weight)
        late_arrivals = attendance["overview"]["lateArrivals"]
        total_days = attendance["overview"]["totalPresentDays"]
        punctuality_rate = max(0, (total_days - late_arrivals) / total_days * 100)
        punctuality_factor = punctuality_rate / 100 * 20
        productivity_score += punctuality_factor
        factors.append(f"Punctuality: {punctuality_rate:.1f}% (Weight: 20%)")
        
        return {
            "overall_score": round(productivity_score, 1),
            "rating": self._get_productivity_rating(productivity_score),
            "factors": factors,
            "recommendations": self._get_productivity_recommendations(productivity_score)
        }

    def _get_productivity_rating(self, score: float) -> str:
        if score >= 85:
            return "Excellent"
        elif score >= 75:
            return "Good"
        elif score >= 65:
            return "Average"
        else:
            return "Needs Improvement"

    def _get_productivity_recommendations(self, score: float) -> List[str]:
        recommendations = []
        if score < 75:
            recommendations.append("Focus on improving attendance rates")
            recommendations.append("Implement attendance monitoring system")
        if score < 65:
            recommendations.append("Consider flexible working arrangements")
            recommendations.append("Review company policies and employee satisfaction")
        return recommendations

# Usage
api = DashboardAPI()
api.set_token("your-jwt-token")

# Get complete dashboard
dashboard = api.get_dashboard(period="month", refresh=True)
print(f"Total Employees: {dashboard['metrics']['totalEmployees']}")
print(f"Attendance Rate: {dashboard['metrics']['avgAttendanceRate']}%")

# Generate attendance report
attendance_report = api.generate_attendance_report("month")
print("\nAttendance Report:")
print(f"Period: {attendance_report['summary']['period']}")
print(f"Average Attendance: {attendance_report['summary']['average_attendance']}%")

for dept in attendance_report['department_performance']:
    print(f"{dept['department']}: {dept['attendance_rate']}% ({dept['status']})")

# Calculate productivity metrics
productivity = api.calculate_productivity_metrics()
print(f"\nProductivity Score: {productivity['overall_score']} ({productivity['rating']})")
for factor in productivity['factors']:
    print(f"- {factor}")

if productivity['recommendations']:
    print("\nRecommendations:")
    for rec in productivity['recommendations']:
        print(f"- {rec}")
```

---

The Dashboard API provides comprehensive, role-based dashboard experiences with real-time metrics, interactive charts, and actionable insights for effective HR and organizational management.
