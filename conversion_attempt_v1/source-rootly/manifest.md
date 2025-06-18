# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various resources, each with its own endpoint and configuration settings. The API allows for data retrieval with pagination and supports incremental synchronization based on a datetime cursor.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v1/",
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
            "name": "dashboards",
            "endpoint": {
                "path": "/dashboards",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page[number]",
                    "limit_param": "page[size]",
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
            "name": "incidents",
            "endpoint": {
                "path": "/incidents",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page[number]",
                    "limit_param": "page[size]",
                    "limit": 50
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 2,
                    "backoff_factor": 5
                }
            }
        },
        // Additional resources can be added here following the same pattern
    ]
})
```

# Authentication

- **Type**: Bearer
- **Token**: Retrieved from `dlt.secrets["api_key"]`

# Resources

Each resource is configured with the following parameters:

- **Resource Name**: A neutral name representing the data being accessed.
- **Endpoint Path**: The API path to access the resource.
- **HTTP Method**: GET (default)
- **Data Selector**: The JSON path to extract data from the response.
- **Pagination Strategy**: Page number-based pagination with configurable page and limit parameters.
- **Error Handling**: Retry logic for handling rate limits and other transient errors.

# Error Handling

- **Retry Logic**: Configured to retry on HTTP status code 429 with a maximum of 2 retries and a constant backoff factor of 5 seconds.
- **Known HTTP Response Codes**: 429 for rate limiting.

# Schema

The schema for each resource is defined with neutral field names and types, ensuring compatibility with the dltHub format. Each resource includes a primary key and supports incremental synchronization based on the `updated_at` field.