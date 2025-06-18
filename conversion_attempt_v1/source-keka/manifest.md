# Overview

This document provides a configuration guide for integrating with a third-party employee management and payroll API using dltHub's REST API source. The integration allows for the automated extraction and synchronization of various data resources, such as employee details, attendance records, and project timesheets, into your preferred destinations for reporting and analytics.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "oauth2",
            "client_id": dlt.secrets["client_id"],
            "client_secret": dlt.secrets["client_secret"],
            "token_url": "https://api.example.com/connect/token"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "clients",
            "endpoint": {
                "path": "/v1/clients",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "pageNumber",
                    "limit_param": "pageSize",
                    "limit": 100
                }
            }
        },
        {
            "name": "projects",
            "endpoint": {
                "path": "/v1/projects",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "pageNumber",
                    "limit_param": "pageSize",
                    "limit": 100
                }
            }
        },
        {
            "name": "employees",
            "endpoint": {
                "path": "/v1/employees",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "pageNumber",
                    "limit_param": "pageSize",
                    "limit": 100
                }
            }
        },
        {
            "name": "attendance",
            "endpoint": {
                "path": "/v1/attendance",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "pageNumber",
                    "limit_param": "pageSize",
                    "limit": 100
                }
            }
        },
        {
            "name": "leave_types",
            "endpoint": {
                "path": "/v1/leavetypes",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "pageNumber",
                    "limit_param": "pageSize",
                    "limit": 100
                }
            }
        },
        {
            "name": "leave_requests",
            "endpoint": {
                "path": "/v1/leaverequests",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "pageNumber",
                    "limit_param": "pageSize",
                    "limit": 100
                }
            }
        },
        {
            "name": "project_timesheets",
            "endpoint": {
                "path": "/v1/timeentries",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "pageNumber",
                    "limit_param": "pageSize",
                    "limit": 100
                }
            }
        }
    ]
})
```

## Authentication

The integration uses OAuth2 authentication. The following secrets are required:

- `client_id`: Your client identifier for authentication.
- `client_secret`: Your client secret for secure authentication.
- `token_url`: The URL to obtain the access token.

## Resources

### Clients
- **Endpoint Path**: `/v1/clients`
- **Pagination**: Page number-based with `pageNumber` and `pageSize` parameters.
- **Data Selector**: `data`

### Projects
- **Endpoint Path**: `/v1/projects`
- **Pagination**: Page number-based with `pageNumber` and `pageSize` parameters.
- **Data Selector**: `data`

### Employees
- **Endpoint Path**: `/v1/employees`
- **Pagination**: Page number-based with `pageNumber` and `pageSize` parameters.
- **Data Selector**: `data`

### Attendance
- **Endpoint Path**: `/v1/attendance`
- **Pagination**: Page number-based with `pageNumber` and `pageSize` parameters.
- **Data Selector**: `data`

### Leave Types
- **Endpoint Path**: `/v1/leavetypes`
- **Pagination**: Page number-based with `pageNumber` and `pageSize` parameters.
- **Data Selector**: `data`

### Leave Requests
- **Endpoint Path**: `/v1/leaverequests`
- **Pagination**: Page number-based with `pageNumber` and `pageSize` parameters.
- **Data Selector**: `data`

### Project Timesheets
- **Endpoint Path**: `/v1/timeentries`
- **Pagination**: Page number-based with `pageNumber` and `pageSize` parameters.
- **Data Selector**: `data`

## Error Handling

The integration includes retry logic for handling transient errors. The following HTTP status codes will trigger a retry:

- 429 (Too Many Requests)
- 500 (Internal Server Error)
- 502 (Bad Gateway)
- 503 (Service Unavailable)
- 504 (Gateway Timeout)

The retry mechanism will attempt up to 3 retries with an exponential backoff factor of 2.

## Schema

The schema for each resource is defined with neutral field names and types. For example, the `Clients` resource includes fields such as `id`, `name`, and `billingAddress`. All fields are nullable to accommodate varying data availability.