# Overview

This document provides a configuration example for integrating with a third-party project management and CRM API using dltHub's REST API source. The integration allows for seamless data synchronization, enabling efficient data extraction and analysis. The API supports various resources such as job records, contact information, task details, and more.

## Configuration Example

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
                "data_selector": "results",
                "paginator": {
                    "type": "offset",
                    "limit": 1000,
                    "offset_param": "from"
                }
            }
        },
        {
            "name": "contacts",
            "endpoint": {
                "path": "/v1/contacts",
                "data_selector": "results",
                "paginator": {
                    "type": "offset",
                    "limit": 1000,
                    "offset_param": "from"
                }
            }
        },
        {
            "name": "tasks",
            "endpoint": {
                "path": "/v1/tasks",
                "data_selector": "results",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "from",
                    "size_param": "size"
                }
            }
        },
        {
            "name": "activities",
            "endpoint": {
                "path": "/v1/activities",
                "data_selector": "activity",
                "paginator": {
                    "type": "offset",
                    "limit": 1000,
                    "offset_param": "from",
                    "size_param": "size"
                }
            }
        },
        {
            "name": "files",
            "endpoint": {
                "path": "/v1/files",
                "data_selector": "files",
                "paginator": {
                    "type": "offset",
                    "limit": 1000,
                    "offset_param": "from",
                    "size_param": "size"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Bearer
- **Token**: Retrieved from `dlt.secrets["api_key"]`

## Resources

### Jobs
- **Endpoint Path**: `/v1/jobs`
- **Data Selector**: `results`
- **Pagination**: Offset with `limit` of 1000 and `offset_param` as `from`

### Contacts
- **Endpoint Path**: `/v1/contacts`
- **Data Selector**: `results`
- **Pagination**: Offset with `limit` of 1000 and `offset_param` as `from`

### Tasks
- **Endpoint Path**: `/v1/tasks`
- **Data Selector**: `results`
- **Pagination**: Offset with `limit` of 100, `offset_param` as `from`, and `size_param` as `size`

### Activities
- **Endpoint Path**: `/v1/activities`
- **Data Selector**: `activity`
- **Pagination**: Offset with `limit` of 1000, `offset_param` as `from`, and `size_param` as `size`

### Files
- **Endpoint Path**: `/v1/files`
- **Data Selector**: `files`
- **Pagination**: Offset with `limit` of 1000, `offset_param` as `from`, and `size_param` as `size`

## Error Handling

- **Retry Logic**: Implement retry on HTTP status codes 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

The schema for each resource includes fields such as `id`, `name`, `created_by`, and other relevant attributes. All fields are generalized and do not include any proprietary or branded terms.