# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source format. The integration supports various resources, each with its own endpoint and pagination strategy. The API requires authentication via an API key, which is included in the request headers.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "api_key",
            "name": "api_key",
            "api_key": dlt.secrets["api_key"],
            "location": "header"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "tags",
            "endpoint": {
                "path": "/v1/tags",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page[number]",
                    "limit_param": "page[size]",
                    "limit": 10
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 4,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "deals",
            "endpoint": {
                "path": "/v1/deals",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page[number]",
                    "limit_param": "page[size]",
                    "limit": 10
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 4,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "pages",
            "endpoint": {
                "path": "/v1/pages",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page[number]",
                    "limit_param": "page[size]",
                    "limit": 10
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 4,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "tasks",
            "endpoint": {
                "path": "/v1/tasks",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page[number]",
                    "limit_param": "page[size]",
                    "limit": 10
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 4,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/v1/users",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page[number]",
                    "limit_param": "page[size]",
                    "limit": 10
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 4,
                    "backoff_factor": 2
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: 
  - `api_key`: The API key used for authentication.
  - `organization_id`: The organization ID required for API requests.

# Resources

Each resource is defined with a name, endpoint path, pagination strategy, and data selector. The resources include:

- **tags**: `/v1/tags`
- **deals**: `/v1/deals`
- **pages**: `/v1/pages`
- **tasks**: `/v1/tasks`
- **users**: `/v1/users`

# Error Handling

The error handling configuration includes retry logic for HTTP status code 429 (rate limits). The system will attempt up to 4 retries with an exponential backoff factor of 2.

# Schema

The schema for each resource is defined with fields, types, and nullability. Field names are generalized to maintain neutrality. For example, `id`, `name`, `created_at`, etc., are used across resources.