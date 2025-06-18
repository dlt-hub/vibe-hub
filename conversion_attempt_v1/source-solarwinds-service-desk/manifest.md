# Overview

This document provides a vendor-neutral configuration for integrating with a third-party REST API. The integration supports various resources, each with its own endpoint and configuration. The API uses pagination and supports incremental synchronization based on datetime fields.

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
            "name": "hardwares",
            "endpoint": {
                "path": "/hardwares.json",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 50
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 2,
                    "backoff_factor": 5
                }
            }
        },
        {
            "name": "risks",
            "endpoint": {
                "path": "/risks.json",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 50
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 2,
                    "backoff_factor": 5
                }
            }
        },
        {
            "name": "audits",
            "endpoint": {
                "path": "/audits.json",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 50
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 2,
                    "backoff_factor": 5
                }
            }
        },
        // Additional resources follow the same pattern
    ]
})
```

# Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: 
  - `api_key`: The API key used for authentication, injected into the header.

# Resources

Each resource is configured with the following parameters:

- **Resource Name**: Abstracted from the original name.
- **Endpoint Path**: The path to the resource endpoint.
- **HTTP Method**: GET (default).
- **Pagination Strategy**: Page number-based pagination with parameters `page` and `per_page`.
- **Data Selector**: The field path to extract data from the response.

# Error Handling

- **Retry/Backoff Logic**: 
  - Retries on HTTP status code 429 (rate limit).
  - Maximum of 2 retries with a constant backoff of 5 seconds.

# Schema

Each resource has a schema defined with fields, types, and nullability. Field names are generalized to maintain neutrality. For example, `id`, `name`, `created_at`, and `updated_at` are common fields across resources.