# HRMS Backend API Documentation

Welcome to the comprehensive API documentation for the Human Resource Management System (HRMS) backend. This documentation covers all available endpoints, authentication, request/response formats, and usage examples.

## Overview

This HRMS API provides complete functionality for managing:
- 👥 **Employee Management** - CRUD operations for employees
- 📅 **Attendance Tracking** - Check-in/out and attendance records
- 🏖️ **Leave Management** - Leave requests and approval workflows
- 💰 **Payroll Processing** - Salary calculations and payslip generation
- 🏢 **Asset Management** - Company asset tracking and assignment
- 📄 **Document Management** - File storage and sharing
- 💸 **Expense Tracking** - Expense claims and approval process
- ⏰ **Timesheet Management** - Time tracking with project integration
- 🔔 **Notifications** - System messaging and alerts
- 🚀 **Employee Onboarding** - Workflow and task management
- 📊 **Dashboard Analytics** - Role-based insights and metrics
- 📈 **Reporting** - Advanced analytics and custom reports

## Base URL

```
https://api.your-domain.com/api
```

## Authentication

All API endpoints require JWT authentication except for login. Include the token in the Authorization header:

```http
Authorization: Bearer <your-jwt-token>
```

## Quick Start

1. **Authentication**: Login to get JWT token
2. **Get Profile**: Fetch your user profile
3. **Check Permissions**: Verify role-based access
4. **Start Using APIs**: Make requests to various endpoints

## API Structure

- **Consistent Response Format**: All responses follow standardized format
- **Role-Based Access Control**: Different permissions for admin, hr, manager, employee
- **Pagination**: List endpoints support pagination parameters
- **Filtering & Search**: Advanced filtering options available
- **Validation**: Comprehensive input validation using Joi schemas
- **Error Handling**: Detailed error messages and proper HTTP status codes

## Navigation

Use the sidebar to navigate through different API sections:

- [Authentication](authentication.md) - Login and token management
- [Employees](employees.md) - Employee CRUD operations
- [Attendance](attendance.md) - Attendance tracking
- [Leave](leave.md) - Leave management
- [Payroll](payroll.md) - Salary and payroll processing
- [Assets](assets.md) - Asset management
- [Documents](documents.md) - Document handling
- [Expenses](expenses.md) - Expense management
- [Timesheets](timesheets.md) - Time tracking
- [Notifications](notifications.md) - System notifications
- [Onboarding](onboarding.md) - Employee onboarding
- [Dashboard](dashboard.md) - Analytics dashboard
- [Reports](reports.md) - Reporting and analytics

## Support

For API support or questions, please contact:
- Email: api-support@your-domain.com
- Documentation: This GitBook
- Status Page: status.your-domain.com
# hrms-docs
