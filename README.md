# 🏠 Upsurge HRMS API Documentation

Welcome to the comprehensive API documentation for Upsurge HRMS - a complete Human Resource Management System backend built with NestJS and PostgreSQL.

## 🌟 Overview

Upsurge HRMS provides a robust, scalable, and feature-rich backend API for managing all aspects of human resource operations. From employee onboarding to payroll processing, our API covers the complete HR lifecycle.

### 🎯 Base URL
```
https://api.upsurgemedia.in
```

### 🔗 Quick Links
- **API Base URL**: `https://api.upsurgemedia.in`
- **Swagger Documentation**: `https://api.upsurgemedia.in/api`
- **Health Check**: `https://api.upsurgemedia.in/health`

## ✨ Key Features

### 👥 **User & Employee Management**
- Multi-role user system (Super Admin, Admin, Manager, Employee)
- Comprehensive employee profiles with personal and professional details
- Department and organizational hierarchy management
- Employee onboarding workflows

### ⏰ **Time & Attendance**
- Real-time attendance tracking with check-in/check-out
- Location-based attendance verification
- Overtime calculation and management
- Attendance analytics and reporting

### 🏖️ **Leave Management**
- Flexible leave types configuration
- Multi-level approval workflows
- Leave balance tracking and carry-forward
- Calendar integration

### 💰 **Payroll & Finance**
- Automated payroll processing
- Salary components and deductions management
- Tax calculations and compliance
- Expense tracking and reimbursements

### 🔔 **Communication & Notifications**
- Real-time notification system
- Email and push notification support
- Announcement and messaging capabilities
- Activity feeds and updates

### 📊 **Analytics & Reporting**
- Comprehensive dashboard with role-based views
- Advanced search and filtering capabilities
- Custom report generation
- Data export in multiple formats

### ⚙️ **System Administration**
- Multi-tenant company support
- Configurable system settings
- Asset and document management
- Security and audit logging

## 🚀 Getting Started

### Prerequisites
- Node.js 18+ and npm
- PostgreSQL 14+
- Redis (optional, for caching)

### Quick Start
1. **Authentication**: All API endpoints require JWT authentication
2. **Base URL**: Use `https://api.upsurgemedia.in` as the base URL
3. **Headers**: Include `Authorization: Bearer <token>` in all requests
4. **Content-Type**: Use `application/json` for request bodies

### Example Request
```bash
curl -X GET "https://api.upsurgemedia.in/employees" \
  -H "Authorization: Bearer your-jwt-token" \
  -H "Content-Type: application/json"
```

## 📋 API Categories

### Core APIs
- **Users**: User authentication and profile management
- **Companies**: Multi-tenant company operations
- **Departments**: Organizational structure management
- **Employees**: Employee lifecycle management

### Attendance & Time
- **Attendance**: Daily attendance tracking and management
- **Leave**: Leave applications and approvals
- **Reports**: Time and attendance reporting

### HR Management
- **Payroll**: Salary processing and management
- **Expenses**: Expense tracking and reimbursements
- **Onboarding**: Employee onboarding workflows
- **Documents**: Document management system

### System Features
- **Dashboard**: Role-based dashboard data
- **Search**: Global search functionality
- **Organization**: Organizational hierarchy and structure
- **Settings**: System and user preferences
- **Notifications**: Real-time notification system
- **Assets**: Company asset management

## 🔐 Security

### Authentication
- JWT-based authentication with refresh tokens
- Role-based access control (RBAC)
- Multi-factor authentication support
- Session management and timeout

### Data Protection
- End-to-end encryption for sensitive data
- Audit logging for all operations
- Data retention and privacy compliance
- Regular security updates and patches

## 📞 Support

### Technical Support
- **Email**: support@upsurgemedia.com
- **Documentation Issues**: Create an issue in our GitHub repository
- **API Questions**: Contact our technical team

### Rate Limits
- **Free Tier**: 1000 requests/hour
- **Professional**: 10,000 requests/hour
- **Enterprise**: Unlimited with custom limits

### Status Page
Monitor API uptime and performance at our status page: `https://status.upsurgemedia.in`

## 🔄 Versioning

We use semantic versioning for our API. The current version is `v1`. Breaking changes will be introduced in new major versions with proper migration guides.

### API Versioning
- Current Version: `v1`
- Version Header: Include `API-Version: v1` in requests
- Deprecation Notice: 6 months advance notice for breaking changes

## 📝 Changelog

Stay updated with the latest changes, improvements, and bug fixes in our [changelog](reference/migration-guide.md).

---

**Ready to get started?** Head over to our [Quick Start Guide](getting-started/quick-start.md) to begin integrating with the Upsurge HRMS API.
