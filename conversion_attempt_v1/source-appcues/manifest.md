# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various resources, each with its own endpoint and configuration. The API uses basic authentication and supports incremental synchronization based on datetime cursors.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v2/",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["username"],
            "password": dlt.secrets["password"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "launchpads",
            "endpoint": {
                "path": "/accounts/{account_id}/launchpads",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "flows",
            "endpoint": {
                "path": "/accounts/{account_id}/flows",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "banners",
            "endpoint": {
                "path": "/accounts/{account_id}/banners",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "checklists",
            "endpoint": {
                "path": "/accounts/{account_id}/checklists",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "pins",
            "endpoint": {
                "path": "/accounts/{account_id}/pins",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "tags",
            "endpoint": {
                "path": "/accounts/{account_id}/tags",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "segments",
            "endpoint": {
                "path": "/accounts/{account_id}/segments",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Basic
- **Secrets**: 
  - `username`: The username for API access.
  - `password`: The password for API access.

## Resources

### Launchpads
- **Endpoint Path**: `/accounts/{account_id}/launchpads`
- **Primary Key**: `id`
- **Pagination**: Single page
- **Data Selector**: `data`

### Flows
- **Endpoint Path**: `/accounts/{account_id}/flows`
- **Primary Key**: `id`
- **Pagination**: Single page
- **Data Selector**: `data`

### Banners
- **Endpoint Path**: `/accounts/{account_id}/banners`
- **Primary Key**: `id`
- **Pagination**: Single page
- **Data Selector**: `data`

### Checklists
- **Endpoint Path**: `/accounts/{account_id}/checklists`
- **Primary Key**: `id`
- **Pagination**: Single page
- **Data Selector**: `data`

### Pins
- **Endpoint Path**: `/accounts/{account_id}/pins`
- **Primary Key**: `id`
- **Pagination**: Single page
- **Data Selector**: `data`

### Tags
- **Endpoint Path**: `/accounts/{account_id}/tags`
- **Primary Key**: `id`
- **Pagination**: Single page
- **Data Selector**: `data`

### Segments
- **Endpoint Path**: `/accounts/{account_id}/segments`
- **Primary Key**: `id`
- **Pagination**: Single page
- **Data Selector**: `data`

## Error Handling

- **Retry on Status Codes**: 429
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

Each resource has its own schema, which includes fields such as `id`, `created_at`, `updated_at`, and other relevant fields. The schemas are designed to be flexible and accommodate various data types, including strings, numbers, and booleans.