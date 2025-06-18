# Overview

This document provides a configuration guide for integrating with a third-party human resources API using dltHub's REST API source. The integration allows for seamless extraction of HR data through multiple configurable resources, including custom reports, employee directories, metadata fields, time-off requests, timesheet entries, and employee details.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/api/gateway.php/{subdomain}/v1/",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["api_key"],
            "password": "x"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "custom_reports",
            "endpoint": {
                "path": "/reports/custom",
                "method": "POST",
                "data_selector": "employees"
            }
        },
        {
            "name": "employee_directory",
            "endpoint": {
                "path": "/employees/directory",
                "method": "GET",
                "data_selector": "employees"
            }
        },
        {
            "name": "meta_fields",
            "endpoint": {
                "path": "/meta/fields",
                "method": "GET",
                "data_selector": []
            }
        },
        {
            "name": "time_off_requests",
            "endpoint": {
                "path": "/time_off/requests/",
                "method": "GET",
                "data_selector": [],
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "created",
                    "start_time_option": {
                        "field_name": "start",
                        "inject_into": "request_parameter"
                    },
                    "end_time_option": {
                        "field_name": "end",
                        "inject_into": "request_parameter"
                    }
                }
            }
        },
        {
            "name": "timesheet_entries",
            "endpoint": {
                "path": "/time_tracking/timesheet_entries",
                "method": "GET",
                "data_selector": [],
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "start",
                    "start_time_option": {
                        "field_name": "start",
                        "inject_into": "request_parameter"
                    },
                    "end_time_option": {
                        "field_name": "end",
                        "inject_into": "request_parameter"
                    }
                }
            }
        },
        {
            "name": "employees",
            "endpoint": {
                "path": "/employees/{id}",
                "method": "GET",
                "data_selector": [],
                "partition_router": {
                    "type": "substream",
                    "parent_key": "id",
                    "partition_field": "id",
                    "parent_stream": "employee_directory"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Basic Authentication
- **Username**: API Key (stored securely in dlt.secrets)
- **Password**: Static value "x"

## Resources

### Custom Reports
- **Endpoint Path**: `/reports/custom`
- **HTTP Method**: POST
- **Data Selector**: `employees`

### Employee Directory
- **Endpoint Path**: `/employees/directory`
- **HTTP Method**: GET
- **Data Selector**: `employees`

### Meta Fields
- **Endpoint Path**: `/meta/fields`
- **HTTP Method**: GET
- **Data Selector**: []

### Time Off Requests
- **Endpoint Path**: `/time_off/requests/`
- **HTTP Method**: GET
- **Data Selector**: []
- **Pagination**: Cursor-based using `created` field

### Timesheet Entries
- **Endpoint Path**: `/time_tracking/timesheet_entries`
- **HTTP Method**: GET
- **Data Selector**: []
- **Pagination**: Cursor-based using `start` field

### Employees
- **Endpoint Path**: `/employees/{id}`
- **HTTP Method**: GET
- **Data Selector**: []
- **Partitioning**: Substream partitioning based on `id` from the employee directory

## Error Handling

- **Retry Logic**: Implement retry on specific HTTP status codes (e.g., 429, 500, 502, 503, 504)
- **Backoff Strategy**: Exponential backoff with a factor of 2
- **Max Retries**: 3 attempts

## Schema

- **Custom Reports**: Dynamic fields based on configuration
- **Employee Directory**: Includes fields like `id`, `firstName`, `lastName`, `department`, etc.
- **Meta Fields**: Includes fields like `id`, `name`, `alias`
- **Time Off Requests**: Includes fields like `id`, `created`, `employeeId`, `start`, `end`
- **Timesheet Entries**: Includes fields like `id`, `start`, `end`, `employeeId`
- **Employees**: Includes fields like `id`, `_cursor`

This configuration provides a comprehensive setup for extracting HR data from a third-party API using dltHub's REST API source, ensuring a clean and vendor-neutral integration.