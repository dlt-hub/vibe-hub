# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration allows users to extract data such as feedback, suggestions, comments, and support metrics, ensuring that new or updated data is captured without duplication. The setup is designed for easy integration with the API to keep customer insights up to date.

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
            "name": "forums",
            "endpoint": {
                "path": "/v1/forums",
                "data_selector": "forums",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "cursor",
                    "cursor_path": "pagination.cursor",
                    "stop_condition": "pagination.cursor is not defined"
                }
            }
        },
        {
            "name": "external_accounts",
            "endpoint": {
                "path": "/v1/external_accounts",
                "data_selector": "external_accounts",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "cursor",
                    "cursor_path": "pagination.cursor",
                    "stop_condition": "pagination.cursor is not defined"
                }
            }
        },
        {
            "name": "external_users",
            "endpoint": {
                "path": "/v1/external_users",
                "data_selector": "external_users",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "cursor",
                    "cursor_path": "pagination.cursor",
                    "stop_condition": "pagination.cursor is not defined"
                }
            }
        },
        {
            "name": "features",
            "endpoint": {
                "path": "/v1/features",
                "data_selector": "features",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "cursor",
                    "cursor_path": "pagination.cursor",
                    "stop_condition": "pagination.cursor is not defined"
                }
            }
        },
        {
            "name": "feedback_records",
            "endpoint": {
                "path": "/v1/feedback_records",
                "data_selector": "feedback_records",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "cursor",
                    "cursor_path": "pagination.cursor",
                    "stop_condition": "pagination.cursor is not defined"
                }
            }
        },
        {
            "name": "suggestions",
            "endpoint": {
                "path": "/v1/suggestions",
                "data_selector": "suggestions",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "cursor",
                    "cursor_path": "pagination.cursor",
                    "stop_condition": "pagination.cursor is not defined"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer
- **Token**: Retrieved from `dlt.secrets["api_key"]`

# Resources

Each resource is configured with the following parameters:

- **Resource Name**: Abstracted from the original stream name.
- **Endpoint Path**: The path to the resource in the API.
- **Data Selector**: The field path to extract data from the response.
- **Pagination Strategy**: Cursor-based pagination using a cursor parameter and path.

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Factor**: Exponential backoff with a factor of 2.
- **Max Retries**: 3 retries before failing.

# Schema

The schema for each resource is defined with fields, types, and nullability. Field names are generalized to ensure neutrality. For example, `id` and `updated_at` are common fields across resources.