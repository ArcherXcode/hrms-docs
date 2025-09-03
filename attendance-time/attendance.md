# ⏰ Attendance API

The Attendance API provides comprehensive time tracking and attendance management functionality including check-in/check-out, location-based attendance, break management, and attendance reporting.

## Base Endpoint

```
https://api.upsurgemedia.in/attendance
```

## Overview

The Attendance API handles all aspects of employee time tracking, from basic check-in/check-out functionality to advanced features like location verification, break tracking, and comprehensive attendance analytics.

## Attendance Models

```typescript
interface AttendanceRecord {
  id: string;                    // UUID
  employeeId: string;            // Employee ID
  companyId: string;             // Company ID
  date: Date;                    // Attendance date (YYYY-MM-DD)
  
  // Check-in Information
  checkInTime?: Date;            // Check-in timestamp
  checkInLocation?: string;      // Check-in location
  checkInDevice?: string;        // Device used for check-in
  checkInPhoto?: string;         // Optional check-in photo
  checkInNotes?: string;         // Check-in notes
  
  // Check-out Information
  checkOutTime?: Date;           // Check-out timestamp
  checkOutLocation?: string;     // Check-out location
  checkOutDevice?: string;       // Device used for check-out
  checkOutPhoto?: string;        // Optional check-out photo
  checkOutNotes?: string;        // Check-out notes
  
  // Calculated Fields
  workingHours?: number;         // Total working hours
  lateMinutes?: number;          // Minutes late for check-in
  earlyMinutes?: number;         // Minutes early for check-out
  overtimeHours?: number;        // Overtime hours
  
  // Break Information
  breaks?: BreakRecord[];        // Break records
  totalBreakTime?: number;       // Total break time in minutes
  
  // Status and Validation
  status: AttendanceStatus;      // Present, absent, late, etc.
  isValidated: boolean;          // Manual validation status
  validatedBy?: string;          // Validated by user ID
  validatedAt?: Date;            // Validation timestamp
  
  // Location Verification
  isLocationVerified: boolean;   // Location verification status
  locationAccuracy?: number;     // GPS accuracy in meters
  
  // Timestamps
  createdAt: Date;
  updatedAt: Date;
  
  // Relationships
  employee?: Employee;           // Employee details
  company?: Company;             // Company details
}

interface BreakRecord {
  id: string;
  attendanceId: string;
  startTime: Date;               // Break start time
  endTime?: Date;                // Break end time
  duration?: number;             // Break duration in minutes
  type: BreakType;               // Lunch, coffee, personal, etc.
  notes?: string;                // Break notes
  createdAt: Date;
  updatedAt: Date;
}

enum AttendanceStatus {
  PRESENT = 'present',
  ABSENT = 'absent',
  LATE = 'late',
  HALF_DAY = 'half_day',
  WORK_FROM_HOME = 'work_from_home',
  ON_LEAVE = 'on_leave',
  HOLIDAY = 'holiday'
}

enum BreakType {
  LUNCH = 'lunch',
  COFFEE = 'coffee',
  PERSONAL = 'personal',
  MEETING = 'meeting',
  OTHER = 'other'
}
```

## Check-in/Check-out Endpoints

### Check-in

Record employee arrival and start work session.

**Endpoint:** `POST /attendance/check-in`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "location": "Main Office - Floor 3",
  "device": "Mobile App",
  "notes": "Starting work on project X",
  "coordinates": {
    "latitude": 37.7749,
    "longitude": -122.4194,
    "accuracy": 5
  },
  "photo": "base64-encoded-image-data"
}
```

**Response:**
```json
{
  "data": {
    "id": "attendance-uuid",
    "employeeId": "employee-uuid",
    "date": "2025-09-03",
    "checkInTime": "2025-09-03T09:15:00Z",
    "checkInLocation": "Main Office - Floor 3",
    "checkInDevice": "Mobile App",
    "status": "late",
    "lateMinutes": 15,
    "isLocationVerified": true,
    "locationAccuracy": 5,
    "employee": {
      "employeeId": "EMP001",
      "user": {
        "fullName": "John Doe"
      }
    }
  },
  "message": "Check-in recorded successfully"
}
```

**Example:**
```bash
curl -X POST "https://api.upsurgemedia.in/attendance/check-in" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "location": "Home Office",
    "device": "Web Browser",
    "notes": "Working from home today"
  }'
```

### Check-out

Record employee departure and end work session.

**Endpoint:** `POST /attendance/check-out`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "location": "Main Office - Parking",
  "device": "Mobile App",
  "notes": "Completed all tasks for today",
  "coordinates": {
    "latitude": 37.7749,
    "longitude": -122.4194,
    "accuracy": 8
  }
}
```

**Response:**
```json
{
  "data": {
    "id": "attendance-uuid",
    "employeeId": "employee-uuid",
    "date": "2025-09-03",
    "checkInTime": "2025-09-03T09:15:00Z",
    "checkOutTime": "2025-09-03T18:30:00Z",
    "workingHours": 8.75,
    "overtimeHours": 0.75,
    "status": "present",
    "checkOutLocation": "Main Office - Parking",
    "checkOutDevice": "Mobile App",
    "isLocationVerified": true
  },
  "message": "Check-out recorded successfully"
}
```

### Get Current Session

Get current attendance session information.

**Endpoint:** `GET /attendance/current-session`

**Headers:** `Authorization: Bearer <token>`

**Response:**
```json
{
  "data": {
    "id": "attendance-uuid",
    "date": "2025-09-03",
    "checkInTime": "2025-09-03T09:15:00Z",
    "checkInLocation": "Main Office",
    "currentStatus": "checked_in",
    "workingHours": 6.25,
    "currentBreak": null,
    "totalBreakTime": 45,
    "employee": {
      "employeeId": "EMP001",
      "user": {
        "fullName": "John Doe"
      }
    }
  }
}
```

### Update Check-in/Check-out

Update existing check-in or check-out record (Admin/HR only).

**Endpoint:** `PATCH /attendance/{id}`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "checkInTime": "2025-09-03T09:00:00Z",
  "checkOutTime": "2025-09-03T18:00:00Z",
  "notes": "Time adjusted by HR",
  "validatedBy": "hr-user-uuid"
}
```

**Response:**
```json
{
  "data": {
    "id": "attendance-uuid",
    "checkInTime": "2025-09-03T09:00:00Z",
    "checkOutTime": "2025-09-03T18:00:00Z",
    "workingHours": 8.25,
    "isValidated": true,
    "validatedBy": "hr-user-uuid",
    "validatedAt": "2025-09-03T20:00:00Z",
    "updatedAt": "2025-09-03T20:00:00Z"
  },
  "message": "Attendance record updated successfully"
}
```

## Break Management

### Start Break

Start a break session during work hours.

**Endpoint:** `POST /attendance/break/start`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "type": "lunch",
  "notes": "Going for lunch break"
}
```

**Response:**
```json
{
  "data": {
    "id": "break-uuid",
    "attendanceId": "attendance-uuid",
    "startTime": "2025-09-03T13:00:00Z",
    "type": "lunch",
    "notes": "Going for lunch break"
  },
  "message": "Break started successfully"
}
```

### End Break

End the current break session.

**Endpoint:** `POST /attendance/break/end`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "notes": "Back from lunch"
}
```

**Response:**
```json
{
  "data": {
    "id": "break-uuid",
    "attendanceId": "attendance-uuid",
    "startTime": "2025-09-03T13:00:00Z",
    "endTime": "2025-09-03T13:45:00Z",
    "duration": 45,
    "type": "lunch",
    "notes": "Back from lunch"
  },
  "message": "Break ended successfully"
}
```

### Get Break History

Get break history for a specific attendance record.

**Endpoint:** `GET /attendance/{id}/breaks`

**Headers:** `Authorization: Bearer <token>`

**Response:**
```json
{
  "data": [
    {
      "id": "break-uuid-1",
      "startTime": "2025-09-03T10:30:00Z",
      "endTime": "2025-09-03T10:45:00Z",
      "duration": 15,
      "type": "coffee",
      "notes": "Coffee break"
    },
    {
      "id": "break-uuid-2",
      "startTime": "2025-09-03T13:00:00Z",
      "endTime": "2025-09-03T13:45:00Z",
      "duration": 45,
      "type": "lunch",
      "notes": "Lunch break"
    }
  ],
  "totalBreakTime": 60
}
```

## Attendance Records

### Get Attendance Records

Retrieve attendance records with filtering and pagination.

**Endpoint:** `GET /attendance`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `employeeId` (string): Filter by employee
- `startDate` (string): Start date (YYYY-MM-DD)
- `endDate` (string): End date (YYYY-MM-DD)
- `status` (string): Filter by attendance status
- `departmentId` (string): Filter by department
- `page` (number): Page number
- `limit` (number): Items per page
- `includeBreaks` (boolean): Include break information

**Response:**
```json
{
  "data": [
    {
      "id": "attendance-uuid",
      "employeeId": "employee-uuid",
      "date": "2025-09-03",
      "checkInTime": "2025-09-03T09:15:00Z",
      "checkOutTime": "2025-09-03T18:30:00Z",
      "workingHours": 8.75,
      "lateMinutes": 15,
      "overtimeHours": 0.75,
      "status": "present",
      "totalBreakTime": 60,
      "employee": {
        "employeeId": "EMP001",
        "user": {
          "fullName": "John Doe"
        },
        "department": {
          "name": "Engineering"
        }
      },
      "breaks": [
        {
          "type": "lunch",
          "duration": 45
        }
      ]
    }
  ],
  "total": 145,
  "page": 1,
  "limit": 10,
  "summary": {
    "totalWorkingHours": 1248.5,
    "averageWorkingHours": 8.6,
    "totalOvertimeHours": 125.25,
    "presentDays": 142,
    "absentDays": 3
  }
}
```

**Example:**
```bash
curl -X GET "https://api.upsurgemedia.in/attendance?startDate=2025-09-01&endDate=2025-09-30&employeeId=emp-uuid" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

### Get Attendance by Employee

Get attendance records for a specific employee.

**Endpoint:** `GET /attendance/employee/{employeeId}`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `startDate` (string): Start date
- `endDate` (string): End date
- `includeStatistics` (boolean): Include summary statistics

**Response:**
```json
{
  "data": [
    {
      "id": "attendance-uuid",
      "date": "2025-09-03",
      "checkInTime": "2025-09-03T09:15:00Z",
      "checkOutTime": "2025-09-03T18:30:00Z",
      "workingHours": 8.75,
      "status": "present",
      "lateMinutes": 15,
      "overtimeHours": 0.75
    }
  ],
  "employee": {
    "employeeId": "EMP001",
    "user": {
      "fullName": "John Doe"
    }
  },
  "statistics": {
    "totalDays": 20,
    "presentDays": 18,
    "absentDays": 2,
    "lateDays": 3,
    "averageWorkingHours": 8.4,
    "totalOvertimeHours": 12.5,
    "attendancePercentage": 90
  }
}
```

### Get Today's Attendance

Get attendance records for today across the company (Admin/HR only).

**Endpoint:** `GET /attendance/today`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `departmentId` (string): Filter by department
- `status` (string): Filter by status

**Response:**
```json
{
  "data": [
    {
      "employee": {
        "id": "employee-uuid",
        "employeeId": "EMP001",
        "user": {
          "fullName": "John Doe"
        },
        "department": {
          "name": "Engineering"
        }
      },
      "attendance": {
        "id": "attendance-uuid",
        "checkInTime": "2025-09-03T09:15:00Z",
        "status": "present",
        "lateMinutes": 15,
        "workingHours": 6.25,
        "isOnBreak": false
      }
    }
  ],
  "summary": {
    "totalEmployees": 45,
    "presentEmployees": 38,
    "absentEmployees": 4,
    "lateEmployees": 6,
    "onBreakEmployees": 5,
    "workFromHomeEmployees": 8
  }
}
```

## Attendance Analytics

### Get Attendance Statistics

Get comprehensive attendance analytics and statistics.

**Endpoint:** `GET /attendance/statistics`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `period` (string): Time period (today, week, month, quarter, year)
- `departmentId` (string): Filter by department
- `employeeId` (string): Filter by employee
- `compareWithPrevious` (boolean): Include comparison with previous period

**Response:**
```json
{
  "data": {
    "overview": {
      "totalWorkingDays": 22,
      "totalPresentDays": 890,
      "totalAbsentDays": 88,
      "averageAttendanceRate": 91.0,
      "totalWorkingHours": 7120.5,
      "totalOvertimeHours": 342.25
    },
    "trends": {
      "daily": [
        {
          "date": "2025-09-01",
          "presentEmployees": 42,
          "totalEmployees": 45,
          "attendanceRate": 93.3
        }
      ],
      "weekly": [
        {
          "week": "2025-W35",
          "attendanceRate": 89.5,
          "averageWorkingHours": 42.5
        }
      ]
    },
    "departments": [
      {
        "department": "Engineering",
        "attendanceRate": 94.2,
        "averageWorkingHours": 8.8,
        "lateArrivals": 12
      }
    ],
    "topPerformers": [
      {
        "employee": {
          "employeeId": "EMP001",
          "user": {
            "fullName": "John Doe"
          }
        },
        "attendanceRate": 100,
        "averageWorkingHours": 8.5,
        "punctualityScore": 98.5
      }
    ],
    "lateArrivals": {
      "total": 45,
      "averageLateDuration": 18.5,
      "frequentLateEmployees": [
        {
          "employeeId": "EMP010",
          "user": {
            "fullName": "Late Employee"
          },
          "lateCount": 8,
          "averageLateDuration": 25
        }
      ]
    }
  }
}
```

### Get Team Attendance

Get attendance summary for a team/department.

**Endpoint:** `GET /attendance/team/{departmentId}`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `date` (string): Specific date (default: today)
- `includeSubordinates` (boolean): Include subordinate departments

**Response:**
```json
{
  "data": {
    "department": {
      "id": "dept-uuid",
      "name": "Engineering",
      "manager": {
        "user": {
          "fullName": "Jane Smith"
        }
      }
    },
    "summary": {
      "totalEmployees": 15,
      "presentEmployees": 14,
      "absentEmployees": 1,
      "lateEmployees": 2,
      "workFromHomeEmployees": 3,
      "attendanceRate": 93.3
    },
    "employees": [
      {
        "employee": {
          "employeeId": "EMP001",
          "user": {
            "fullName": "John Doe"
          }
        },
        "status": "present",
        "checkInTime": "2025-09-03T09:15:00Z",
        "workingHours": 6.25,
        "isLate": true,
        "lateMinutes": 15
      }
    ]
  }
}
```

## Location-Based Attendance

### Set Work Locations

Configure allowed work locations for attendance (Admin only).

**Endpoint:** `POST /attendance/locations`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "name": "Main Office",
  "address": "123 Business St, San Francisco, CA",
  "coordinates": {
    "latitude": 37.7749,
    "longitude": -122.4194
  },
  "radius": 100,
  "isActive": true
}
```

**Response:**
```json
{
  "data": {
    "id": "location-uuid",
    "name": "Main Office",
    "address": "123 Business St, San Francisco, CA",
    "coordinates": {
      "latitude": 37.7749,
      "longitude": -122.4194
    },
    "radius": 100,
    "isActive": true,
    "createdAt": "2025-09-03T10:00:00Z"
  },
  "message": "Work location created successfully"
}
```

### Get Work Locations

Get all configured work locations.

**Endpoint:** `GET /attendance/locations`

**Headers:** `Authorization: Bearer <token>`

**Response:**
```json
{
  "data": [
    {
      "id": "location-uuid",
      "name": "Main Office",
      "address": "123 Business St, San Francisco, CA",
      "coordinates": {
        "latitude": 37.7749,
        "longitude": -122.4194
      },
      "radius": 100,
      "isActive": true
    },
    {
      "id": "location-uuid-2",
      "name": "Branch Office",
      "address": "456 Tech Ave, Palo Alto, CA",
      "coordinates": {
        "latitude": 37.4419,
        "longitude": -122.1430
      },
      "radius": 150,
      "isActive": true
    }
  ]
}
```

### Verify Location

Verify if coordinates are within allowed work locations.

**Endpoint:** `POST /attendance/verify-location`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "latitude": 37.7749,
  "longitude": -122.4194,
  "accuracy": 5
}
```

**Response:**
```json
{
  "data": {
    "isValid": true,
    "location": {
      "id": "location-uuid",
      "name": "Main Office"
    },
    "distance": 25.5,
    "accuracy": 5
  }
}
```

## Bulk Operations

### Bulk Import Attendance

Import attendance records in bulk (Admin/HR only).

**Endpoint:** `POST /attendance/bulk-import`

**Headers:** `Authorization: Bearer <token>`

**Request Body:**
```json
{
  "records": [
    {
      "employeeId": "EMP001",
      "date": "2025-09-01",
      "checkInTime": "2025-09-01T09:00:00Z",
      "checkOutTime": "2025-09-01T18:00:00Z",
      "status": "present"
    },
    {
      "employeeId": "EMP002",
      "date": "2025-09-01",
      "checkInTime": "2025-09-01T09:15:00Z",
      "checkOutTime": "2025-09-01T17:45:00Z",
      "status": "present"
    }
  ]
}
```

**Response:**
```json
{
  "data": {
    "imported": 2,
    "failed": 0,
    "results": [
      {
        "employeeId": "EMP001",
        "date": "2025-09-01",
        "status": "imported",
        "id": "attendance-uuid-1"
      },
      {
        "employeeId": "EMP002",
        "date": "2025-09-01",
        "status": "imported",
        "id": "attendance-uuid-2"
      }
    ]
  },
  "message": "Bulk import completed successfully"
}
```

### Export Attendance Data

Export attendance data in various formats.

**Endpoint:** `GET /attendance/export`

**Headers:** `Authorization: Bearer <token>`

**Query Parameters:**
- `format` (string): Export format (csv, xlsx, pdf)
- `startDate` (string): Start date
- `endDate` (string): End date
- `employeeId` (string): Filter by employee
- `departmentId` (string): Filter by department

**Response:** File download

**Example:**
```bash
curl -X GET "https://api.upsurgemedia.in/attendance/export?format=csv&startDate=2025-09-01&endDate=2025-09-30" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -o "attendance_report.csv"
```

## Error Responses

### Common Error Codes

#### 400 Bad Request
```json
{
  "error": {
    "code": "ALREADY_CHECKED_IN",
    "message": "Employee is already checked in for today",
    "statusCode": 400
  }
}
```

#### 422 Validation Error
```json
{
  "error": {
    "code": "LOCATION_VERIFICATION_FAILED",
    "message": "Check-in location is outside allowed work areas",
    "details": {
      "distance": 250.5,
      "allowedRadius": 100
    },
    "statusCode": 422
  }
}
```

## Integration Examples

### JavaScript/React

```javascript
class AttendanceService {
  constructor(baseURL = 'https://api.upsurgemedia.in') {
    this.api = axios.create({ baseURL });
  }

  setToken(token) {
    this.api.defaults.headers.common['Authorization'] = `Bearer ${token}`;
  }

  async checkIn(checkInData) {
    const response = await this.api.post('/attendance/check-in', checkInData);
    return response.data.data;
  }

  async checkOut(checkOutData) {
    const response = await this.api.post('/attendance/check-out', checkOutData);
    return response.data.data;
  }

  async getCurrentSession() {
    const response = await this.api.get('/attendance/current-session');
    return response.data.data;
  }

  async startBreak(breakData) {
    const response = await this.api.post('/attendance/break/start', breakData);
    return response.data.data;
  }

  async endBreak(notes) {
    const response = await this.api.post('/attendance/break/end', { notes });
    return response.data.data;
  }

  async getAttendanceRecords(params = {}) {
    const response = await this.api.get('/attendance', { params });
    return response.data;
  }

  async getTodayAttendance() {
    const response = await this.api.get('/attendance/today');
    return response.data;
  }

  async getAttendanceStatistics(params = {}) {
    const response = await this.api.get('/attendance/statistics', { params });
    return response.data.data;
  }

  async verifyLocation(coordinates) {
    const response = await this.api.post('/attendance/verify-location', coordinates);
    return response.data.data;
  }
}

// Usage with geolocation
const attendanceService = new AttendanceService();
attendanceService.setToken('your-jwt-token');

// Check-in with location
navigator.geolocation.getCurrentPosition(async (position) => {
  const checkInData = {
    location: 'Main Office',
    device: 'Web Browser',
    coordinates: {
      latitude: position.coords.latitude,
      longitude: position.coords.longitude,
      accuracy: position.coords.accuracy
    }
  };
  
  try {
    const result = await attendanceService.checkIn(checkInData);
    console.log('Checked in successfully:', result);
  } catch (error) {
    console.error('Check-in failed:', error.response.data);
  }
});
```

### Python

```python
import requests
from datetime import datetime, date
from typing import Dict, List, Optional

class AttendanceAPI:
    def __init__(self, base_url: str = "https://api.upsurgemedia.in"):
        self.base_url = base_url
        self.session = requests.Session()

    def set_token(self, token: str):
        self.session.headers.update({"Authorization": f"Bearer {token}"})

    def check_in(self, check_in_data: Dict) -> Dict:
        response = self.session.post(
            f"{self.base_url}/attendance/check-in",
            json=check_in_data
        )
        response.raise_for_status()
        return response.json()["data"]

    def check_out(self, check_out_data: Dict) -> Dict:
        response = self.session.post(
            f"{self.base_url}/attendance/check-out",
            json=check_out_data
        )
        response.raise_for_status()
        return response.json()["data"]

    def get_attendance_records(self, **params) -> Dict:
        response = self.session.get(
            f"{self.base_url}/attendance",
            params=params
        )
        response.raise_for_status()
        return response.json()

    def get_current_session(self) -> Optional[Dict]:
        response = self.session.get(f"{self.base_url}/attendance/current-session")
        if response.status_code == 404:
            return None
        response.raise_for_status()
        return response.json()["data"]

    def start_break(self, break_type: str, notes: str = "") -> Dict:
        response = self.session.post(
            f"{self.base_url}/attendance/break/start",
            json={"type": break_type, "notes": notes}
        )
        response.raise_for_status()
        return response.json()["data"]

    def end_break(self, notes: str = "") -> Dict:
        response = self.session.post(
            f"{self.base_url}/attendance/break/end",
            json={"notes": notes}
        )
        response.raise_for_status()
        return response.json()["data"]

    def get_attendance_statistics(self, **params) -> Dict:
        response = self.session.get(
            f"{self.base_url}/attendance/statistics",
            params=params
        )
        response.raise_for_status()
        return response.json()["data"]

# Usage
api = AttendanceAPI()
api.set_token("your-jwt-token")

# Check in
check_in_result = api.check_in({
    "location": "Home Office",
    "device": "Python Script",
    "notes": "Working remotely today"
})
print(f"Checked in at: {check_in_result['checkInTime']}")

# Get monthly attendance
monthly_records = api.get_attendance_records(
    startDate="2025-09-01",
    endDate="2025-09-30",
    includeBreaks=True
)
print(f"Total records: {monthly_records['total']}")
```

---

The Attendance API provides comprehensive time tracking with location verification, break management, and detailed analytics for efficient workforce management.
