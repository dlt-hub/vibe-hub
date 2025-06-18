# Overview

This document provides a configuration example for integrating with a third-party REST API using dltHub's REST API source format. The integration supports various resources, each with its own endpoint and pagination strategy. The API requires authentication via an API key, and supports incremental synchronization based on a datetime cursor.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v1/",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["api_key"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "addresses",
            "endpoint": {
                "path": "/addresses",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next_url"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "banks",
            "endpoint": {
                "path": "/bank_accounts",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next_url"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "postcards",
            "endpoint": {
                "path": "/postcards",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next_url"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "templates",
            "endpoint": {
                "path": "/templates",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next_url"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "templates_versions",
            "endpoint": {
                "path": "/templates/{tmpl_id}/versions",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next_url"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "campaigns",
            "endpoint": {
                "path": "/campaigns",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next_url"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "uploads",
            "endpoint": {
                "path": "/uploads",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next_url"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "qr_code_analytics",
            "endpoint": {
                "path": "/qr_code_analytics",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next_url"
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
- **Credentials**: API key (stored securely in dlt.secrets)

## Resources

### Addresses
- **Endpoint Path**: `/addresses`
- **Pagination**: Cursor-based using `next_url`
- **Data Selector**: `data`

### Banks
- **Endpoint Path**: `/bank_accounts`
- **Pagination**: Cursor-based using `next_url`
- **Data Selector**: `data`

### Postcards
- **Endpoint Path**: `/postcards`
- **Pagination**: Cursor-based using `next_url`
- **Data Selector**: `data`

### Templates
- **Endpoint Path**: `/templates`
- **Pagination**: Cursor-based using `next_url`
- **Data Selector**: `data`

### Templates Versions
- **Endpoint Path**: `/templates/{tmpl_id}/versions`
- **Pagination**: Cursor-based using `next_url`
- **Data Selector**: `data`

### Campaigns
- **Endpoint Path**: `/campaigns`
- **Pagination**: Cursor-based using `next_url`
- **Data Selector**: `data`

### Uploads
- **Endpoint Path**: `/uploads`
- **Pagination**: Cursor-based using `next_url`
- **Data Selector**: `data`

### QR Code Analytics
- **Endpoint Path**: `/qr_code_analytics`
- **Pagination**: Cursor-based using `next_url`
- **Data Selector**: `data`

## Error Handling

- **Retry on Status Codes**: 429
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

Each resource has a schema defined with a primary key of `id` or `resource_id` for `qr_code_analytics`. The schemas are flexible and allow additional properties.