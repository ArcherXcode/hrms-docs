# 🏥 Health Check API

The Health Check API provides system status and monitoring endpoints for application health.

## Base URL
```
https://api.upsurgemedia.in/api/v1/health
```

## Endpoints Overview

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/health` | General health check |
| GET | `/health/database` | Database connection status |

## Health Status Responses

### General Health Check
Returns overall application status including:
- Application status
- Database connectivity
- Environment information
- Version details

### Database Health Check
Returns database-specific health information:
- Connection status
- Database name and host
- Response time metrics

## Response Format

### Successful Health Check
```json
{
  "success": true,
  "data": {
    "status": "ok",
    "timestamp": "2025-09-03T14:54:25.982Z",
    "database": {
      "status": "healthy",
      "isConnected": true,
      "database": "upsurge_attendance",
      "host": "localhost",
      "port": 5432
    },
    "environment": "production",
    "version": "1.0.0"
  },
  "timestamp": "2025-09-03T14:54:25.982Z"
}
```

### Failed Health Check
```json
{
  "success": false,
  "data": {
    "status": "error",
    "timestamp": "2025-09-03T14:54:25.982Z",
    "database": {
      "status": "unhealthy",
      "isConnected": false,
      "error": "Connection timeout"
    },
    "environment": "production",
    "version": "1.0.0"
  },
  "timestamp": "2025-09-03T14:54:25.982Z"
}
```

## Usage Examples

### Check Application Health
```bash
curl -X GET "https://api.upsurgemedia.in/api/v1/health"
```

### Check Database Health
```bash
curl -X GET "https://api.upsurgemedia.in/api/v1/health/database"
```

## Monitoring Integration

These endpoints are ideal for:
- Load balancer health checks
- Monitoring system integration
- Automated alerting systems
- DevOps pipeline health verification

## No Authentication Required

Health check endpoints are publicly accessible and do not require authentication tokens.
