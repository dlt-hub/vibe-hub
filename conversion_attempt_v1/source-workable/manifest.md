# Overview

This document provides a configuration example for integrating with a third-party recruitment API using dltHub's REST API source format. The integration supports data synchronization for various resources such as job listings, candidates, stages, and recruiters. The API uses bearer token authentication and supports cursor-based pagination for data retrieval.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["api_key"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "jobs",
            "endpoint": {
                "path": "/v1/jobs",
                "data_selector": "jobs",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_value": "{{ response.paging.next }}",
                    "stop_condition": "{{ 'next' not in response['paging'] }}"
                }
            }
        },
        {
            "name": "candidates",
            "endpoint": {
                "path": "/v1/candidates",
                "data_selector": "candidates",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_value": "{{ response.paging.next }}",
                    "stop_condition": "{{ 'next' not in response['paging'] }}"
                }
            }
        },
        {
            "name": "stages",
            "endpoint": {
                "path": "/v1/stages",
                "data_selector": "stages",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_value": "{{ response.paging.next }}",
                    "stop_condition": "{{ 'next' not in response['paging'] }}"
                }
            }
        },
        {
            "name": "recruiters",
            "endpoint": {
                "path": "/v1/recruiters",
                "data_selector": "recruiters",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_value": "{{ response.paging.next }}",
                    "stop_condition": "{{ 'next' not in response['paging'] }}"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer Token
- **Token**: Retrieved from `dlt.secrets["api_key"]`

# Resources

### Jobs
- **Endpoint Path**: `/v1/jobs`
- **Data Selector**: `jobs`
- **Primary Key**: `id`
- **Pagination**: Cursor-based with a limit of 100 records per page.

### Candidates
- **Endpoint Path**: `/v1/candidates`
- **Data Selector**: `candidates`
- **Primary Key**: `id`
- **Pagination**: Cursor-based with a limit of 100 records per page.

### Stages
- **Endpoint Path**: `/v1/stages`
- **Data Selector**: `stages`
- **Primary Key**: `slug`
- **Pagination**: Cursor-based with a limit of 100 records per page.

### Recruiters
- **Endpoint Path**: `/v1/recruiters`
- **Data Selector**: `recruiters`
- **Primary Key**: `id`
- **Pagination**: Cursor-based with a limit of 100 records per page.

# Error Handling

- **Retry Logic**: Implement retry on HTTP status codes 429, 500, 502, 503, 504 with a maximum of 3 retries and an exponential backoff factor of 2.

# Schema

### Jobs
- **Fields**: `application_url`, `code`, `created_at`, `department`, `department_hierarchy`, `full_title`, `id`, `location`, `shortcode`, `shortlink`, `state`, `title`, `url`
- **Types**: String, Integer, Boolean, Array

### Candidates
- **Fields**: `email`, `headline`, `id`, `name`, `role`
- **Types**: String

### Stages
- **Fields**: `kind`, `name`, `position`, `slug`
- **Types**: String, Number

### Recruiters
- **Fields**: `email`, `id`, `name`
- **Types**: String

This configuration provides a clean and generalized setup for integrating with a recruitment API using dltHub's REST API source format, ensuring a vendor-neutral approach.