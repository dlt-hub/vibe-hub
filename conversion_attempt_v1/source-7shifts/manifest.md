# Overview

This document provides a configuration example for integrating with a third-party scheduling and employee management API using the dltHub REST API source format. The API allows access to various resources such as companies, locations, departments, roles, users, and more, with support for incremental synchronization based on modification timestamps.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["access_token"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "companies",
            "endpoint": {
                "path": "/v2/companies",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "cursor_path": "meta.cursor.next",
                    "stop_condition": "response.get('meta', {}).get('cursor', {}).get('next') is None"
                }
            }
        },
        {
            "name": "locations",
            "endpoint": {
                "path": "/v2/company/{{ company_id }}/locations",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "cursor_path": "meta.cursor.next",
                    "stop_condition": "response.get('meta', {}).get('cursor', {}).get('next') is None"
                }
            }
        },
        {
            "name": "departments",
            "endpoint": {
                "path": "/v2/company/{{ company_id }}/departments",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "cursor_path": "meta.cursor.next",
                    "stop_condition": "response.get('meta', {}).get('cursor', {}).get('next') is None"
                }
            }
        },
        {
            "name": "roles",
            "endpoint": {
                "path": "/v2/company/{{ company_id }}/roles",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "cursor_path": "meta.cursor.next",
                    "stop_condition": "response.get('meta', {}).get('cursor', {}).get('next') is None"
                }
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/v2/company/{{ company_id }}/users",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "cursor_path": "meta.cursor.next",
                    "stop_condition": "response.get('meta', {}).get('cursor', {}).get('next') is None"
                }
            }
        },
        {
            "name": "wages",
            "endpoint": {
                "path": "/v2/company/{{ company_id }}/users/{{ user_id }}/wages",
                "data_selector": "data"
            }
        },
        {
            "name": "assignments",
            "endpoint": {
                "path": "/v2/company/{{ company_id }}/users/{{ user_id }}/assignments",
                "data_selector": "data"
            }
        },
        {
            "name": "location_assignments",
            "endpoint": {
                "path": "/v2/company/{{ company_id }}/users/{{ user_id }}/location_assignments",
                "data_selector": "data"
            }
        },
        {
            "name": "department_assignments",
            "endpoint": {
                "path": "/v2/company/{{ company_id }}/users/{{ user_id }}/department_assignments",
                "data_selector": "data"
            }
        },
        {
            "name": "role_assignments",
            "endpoint": {
                "path": "/v2/company/{{ company_id }}/users/{{ user_id }}/role_assignments",
                "data_selector": "data"
            }
        },
        {
            "name": "time_punches",
            "endpoint": {
                "path": "/v2/company/{{ company_id }}/time_punches",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "cursor_path": "meta.cursor.next",
                    "stop_condition": "response.get('meta', {}).get('cursor', {}).get('next') is None"
                }
            }
        },
        {
            "name": "shifts",
            "endpoint": {
                "path": "/v2/company/{{ company_id }}/shifts",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "cursor_path": "meta.cursor.next",
                    "stop_condition": "response.get('meta', {}).get('cursor', {}).get('next') is None"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Bearer
- **Token**: Use the `access_token` stored in `dlt.secrets`.

## Resources

### Companies
- **Endpoint Path**: `/v2/companies`
- **Primary Key**: `id`
- **Pagination**: Cursor-based with `cursor` parameter and `meta.cursor.next` path.

### Locations
- **Endpoint Path**: `/v2/company/{{ company_id }}/locations`
- **Primary Key**: `id`
- **Pagination**: Cursor-based with `cursor` parameter and `meta.cursor.next` path.

### Departments
- **Endpoint Path**: `/v2/company/{{ company_id }}/departments`
- **Primary Key**: `id`
- **Pagination**: Cursor-based with `cursor` parameter and `meta.cursor.next` path.

### Roles
- **Endpoint Path**: `/v2/company/{{ company_id }}/roles`
- **Primary Key**: `id`
- **Pagination**: Cursor-based with `cursor` parameter and `meta.cursor.next` path.

### Users
- **Endpoint Path**: `/v2/company/{{ company_id }}/users`
- **Primary Key**: `id`
- **Pagination**: Cursor-based with `cursor` parameter and `meta.cursor.next` path.

### Wages
- **Endpoint Path**: `/v2/company/{{ company_id }}/users/{{ user_id }}/wages`
- **Data Selector**: `data`

### Assignments
- **Endpoint Path**: `/v2/company/{{ company_id }}/users/{{ user_id }}/assignments`
- **Data Selector**: `data`

### Location Assignments
- **Endpoint Path**: `/v2/company/{{ company_id }}/users/{{ user_id }}/location_assignments`
- **Data Selector**: `data`

### Department Assignments
- **Endpoint Path**: `/v2/company/{{ company_id }}/users/{{ user_id }}/department_assignments`
- **Data Selector**: `data`

### Role Assignments
- **Endpoint Path**: `/v2/company/{{ company_id }}/users/{{ user_id }}/role_assignments`
- **Data Selector**: `data`

### Time Punches
- **Endpoint Path**: `/v2/company/{{ company_id }}/time_punches`
- **Primary Key**: `id`
- **Pagination**: Cursor-based with `cursor` parameter and `meta.cursor.next` path.

### Shifts
- **Endpoint Path**: `/v2/company/{{ company_id }}/shifts`
- **Primary Key**: `id`
- **Pagination**: Cursor-based with `cursor` parameter and `meta.cursor.next` path.

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504 with a maximum of 3 retries and a backoff factor of 2.

## Schema

Each resource has a schema defined with fields such as `id`, `modified`, and other relevant attributes. The schemas are designed to be flexible with nullable fields to accommodate varying data structures.