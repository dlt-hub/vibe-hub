# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various resources, each with its own endpoint and pagination strategy. The API requires authentication via a bearer token, and the configuration includes error handling for rate limits and other common HTTP errors.

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
            "name": "products",
            "endpoint": {
                "path": "/v1/products",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "pageCursor",
                    "cursor_path": "links.next"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 1
                }
            }
        },
        {
            "name": "features",
            "endpoint": {
                "path": "/v1/features",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "pageCursor",
                    "cursor_path": "links.next"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 1
                }
            }
        },
        {
            "name": "components",
            "endpoint": {
                "path": "/v1/components",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "pageCursor",
                    "cursor_path": "links.next"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 1
                }
            }
        }
        // Additional resources can be added here following the same pattern
    ]
})
```

# Authentication

- **Type**: Bearer
- **Token**: Stored securely in `dlt.secrets["api_key"]`

# Resources

Each resource is configured with a specific endpoint path, data selector, and pagination strategy. The primary key for all resources is `id`, and the default write disposition is `merge`.

- **Resource Name**: Abstracted from the original YAML
- **Endpoint Path**: Generalized to `/v1/resource`
- **HTTP Method**: GET (default)
- **Data Selector**: `data`
- **Pagination Strategy**: Cursor-based with a limit of 100 items per page

# Error Handling

The configuration includes error handling for rate limits and other common HTTP errors:

- **Retry on Status Codes**: [429]
- **Max Retries**: 3
- **Backoff Factor**: 1 second

# Schema

The schema for each resource is defined with neutral field names and types. The primary key is `id`, and additional properties are allowed. The schema is consistent with the original YAML and uses neutral terminology.