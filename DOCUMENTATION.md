# GitBook Documentation Setup

This document explains the GitBook documentation structure for the Upsurge HRMS API.

## 📁 Documentation Structure

```
docs/
├── .gitbook.yaml              # GitBook configuration (YAML format)
├── book.json                  # GitBook configuration (JSON format)
├── README.md                  # Main landing page
├── SUMMARY.md                 # Table of contents
│
├── getting-started/
│   ├── api-overview.md
│   ├── authentication.md
│   └── quick-start.md
│
├── core-apis/
│   ├── users.md
│   ├── companies.md
│   └── employees.md
│
├── attendance-time/
│   ├── attendance.md
│   └── leave.md
│
├── hr-management/
│   ├── departments.md
│   ├── documents.md
│   └── onboarding.md
│
├── payroll-benefits/
│   ├── payroll.md
│   └── expenses.md
│
├── system-features/
│   ├── dashboard.md
│   ├── notifications.md
│   ├── search.md
│   ├── settings.md
│   └── reports.md
│
├── asset-management/
│   └── assets.md
│
├── organization/
│   └── structure.md
│
└── monitoring/
    └── health.md
```

## 🔧 Configuration Files

### `.gitbook.yaml` (Modern GitBook)
- YAML format configuration
- Preferred for newer GitBook versions
- Includes plugins and structure definitions

### `book.json` (Legacy GitBook)
- JSON format configuration
- Backward compatibility
- Detailed plugin configurations

## 🔗 URL Structure

All API URLs in the documentation follow this pattern:
```
https://api.upsurgemedia.in/api/v1/{endpoint}
```

### Key URLs:
- **API Base**: `https://api.upsurgemedia.in`
- **API Version**: `/api/v1`
- **Swagger Docs**: `https://api.upsurgemedia.in/api/docs`
- **Health Check**: `https://api.upsurgemedia.in/api/v1/health`

## 📚 GitBook Features

### Enabled Plugins:
- **Search**: Full-text search across documentation
- **Prism**: Code syntax highlighting
- **Anchors**: Automatic heading anchors
- **Copy Code Button**: One-click code copying
- **Page TOC**: Table of contents for pages
- **Back to Top**: Quick navigation button

### Variables:
- `{{ book.api_base_url }}`: https://api.upsurgemedia.in
- `{{ book.api_version }}`: v1
- `{{ book.swagger_url }}`: https://api.upsurgemedia.in/api/docs

## 🚀 Getting Started with GitBook

### Local Development:
```bash
# Install GitBook CLI
npm install -g gitbook-cli

# Navigate to docs directory
cd docs

# Install dependencies
gitbook install

# Serve locally
gitbook serve
```

### Building Static Site:
```bash
# Build static files
gitbook build

# Output will be in _book/ directory
```

## 📝 Content Guidelines

### 1. **Consistent Formatting**
- Use proper heading hierarchy (H1 → H2 → H3)
- Include code examples with proper syntax highlighting
- Add authentication requirements for each endpoint

### 2. **API Documentation Format**
Each API page should include:
- Base URL
- Endpoint overview table
- Authentication requirements
- Request/response examples
- Error handling

### 3. **Code Examples**
```bash
# Always include working curl examples
curl -X GET "https://api.upsurgemedia.in/api/v1/endpoint" \
  -H "Authorization: Bearer <token>"
```

### 4. **Cross-References**
- Link between related API endpoints
- Reference authentication guide when needed
- Link to Swagger documentation for interactive testing

## 🔄 Maintenance

### Adding New API Endpoints:
1. Create new markdown file in appropriate directory
2. Add entry to `SUMMARY.md`
3. Follow existing format and structure
4. Test all URLs and code examples

### URL Updates:
- All URLs have been standardized to include `/api/v1` prefix
- WebSocket URLs use `wss://` protocol with same prefix structure
- Swagger documentation accessible at `/api/docs`

## 📞 Support

For documentation issues or questions:
- **Technical Issues**: Create GitHub issue
- **Content Updates**: Submit pull request
- **General Questions**: support@upsurgemedia.com
