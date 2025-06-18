# Overview

This document provides a configuration guide for integrating with a third-party analytics API using the dltHub REST API source. The API allows for the retrieval and synchronization of various resources such as projects, apps, metrics, customers, and more. The integration supports incremental synchronization based on datetime cursors and utilizes cursor-based pagination for efficient data retrieval.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v2",
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
            "name": "projects",
            "endpoint": {
                "path": "/projects",
                "data_selector": "items",
                "paginator": {
                    "type": "cursor",
                    "limit": 20,
                    "cursor_param": "starting_after",
                    "stop_condition": "not response.get('next_page', '')"
                }
            }
        },
        {
            "name": "apps",
            "endpoint": {
                "path": "/projects/{project_id}/apps",
                "data_selector": "items",
                "paginator": {
                    "type": "cursor",
                    "limit": 20,
                    "cursor_param": "starting_after",
                    "stop_condition": "not response.get('next_page', '')"
                }
            }
        },
        {
            "name": "metrics_overview",
            "endpoint": {
                "path": "/projects/{project_id}/metrics/overview",
                "data_selector": "metrics",
                "paginator": {
                    "type": "cursor",
                    "limit": 20,
                    "cursor_param": "starting_after",
                    "stop_condition": "not response.get('next_page', '')"
                }
            }
        },
        {
            "name": "customers",
            "endpoint": {
                "path": "/projects/{project_id}/customers",
                "data_selector": "items",
                "paginator": {
                    "type": "cursor",
                    "limit": 20,
                    "cursor_param": "starting_after",
                    "stop_condition": "not response.get('next_page', '')"
                }
            }
        }
        // Additional resources can be added here following the same pattern
    ]
})
```

# Authentication

- **Type**: Bearer
- **Token**: Use the `api_key` stored in `dlt.secrets`.

# Resources

- **Resource Name**: Abstracted from the original stream names.
- **Endpoint Path**: Generalized paths such as `/projects`, `/projects/{project_id}/apps`, etc.
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Cursor-based pagination using the `starting_after` parameter.
- **Data Selector**: Fields like `items` or `metrics` are used to extract data from the response.

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Use exponential backoff with a factor of 2 for retries.
- **Stop Condition**: Pagination stops when the `next_page` field in the response is empty.

# Schema

- **Fields**: Use neutral field names such as `id`, `created_at`, `project_id`, etc.
- **Types**: Include types like string, number, and boolean.
- **Nullability**: Fields can be nullable, indicated by including "null" in the type array.

This configuration guide provides a clean and vendor-neutral setup for integrating with a third-party analytics API using dltHub's REST API source. The focus is on maintaining a generic and reusable configuration pattern without any proprietary or branded content.