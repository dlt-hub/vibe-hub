# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various resources, each with its own endpoint and pagination strategy. The API requires authentication via an API key, and error handling is implemented to manage rate limits and retries.

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
            "name": "public_credentials",
            "endpoint": {
                "path": "/v1/api_public/credentials",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 2
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "webhook_subscriptions",
            "endpoint": {
                "path": "/v1/api_public/webhook_subscriptions",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 2
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "tasks",
            "endpoint": {
                "path": "/v1/tasks/tasks",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 2
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "schedules",
            "endpoint": {
                "path": "/v1/work_schedule/schedules",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 2
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "overlap_periods",
            "endpoint": {
                "path": "/v1/work_schedule/overlap_periods",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 2
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        }
        // Additional resources can be added here following the same pattern
    ]
})
```

# Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: 
  - `api_key`: Stored securely in `dlt.secrets["api_key"]`
- **Location**: Header

# Resources

Each resource is defined with the following parameters:

- **Resource Name**: A neutral name representing the data being accessed.
- **Endpoint Path**: The path to the resource, e.g., `/v1/api_public/credentials`.
- **HTTP Method**: Default is `GET`.
- **Pagination Strategy**: Uses `page_number` with parameters `page` and `limit`.
- **Data Selector**: Extracts data from the `data` field in the response.

# Error Handling

- **Retry Logic**: 
  - Retries on HTTP status code `429` (rate limit).
  - Maximum of 3 retries with an exponential backoff factor of 2.
- **Known HTTP Response Codes**: Handles rate limiting with appropriate retry strategy.

# Schema

- **Fields**: Defined per resource with types and nullability.
- **Primary Key**: `id` is used as the primary key for all resources.
- **Neutral Field Names**: Use generic names like `customer_id` or `user_key` instead of branded terms.