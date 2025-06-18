# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various resources, each with its own endpoint and pagination strategy. The API requires authentication and supports error handling with retry logic.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["username"],
            "password": dlt.secrets["password"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge",
        "endpoint": {
            "params": {
                "limit": 50
            }
        }
    },
    "resources": [
        {
            "name": "projects",
            "endpoint": {
                "path": "/v1/projects",
                "data_selector": "projects",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "priorities",
            "endpoint": {
                "path": "/v1/priorities",
                "data_selector": "priorities",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "plans",
            "endpoint": {
                "path": "/v1/plans",
                "data_selector": "plans",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
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

- **Type**: Basic Authentication
- **Secrets**: 
  - `username`: The username for API access.
  - `password`: The password for API access.

# Resources

Each resource is defined with the following parameters:

- **Resource Name**: A unique identifier for the resource.
- **Endpoint Path**: The API endpoint path for the resource.
- **HTTP Method**: GET (default).
- **Data Selector**: The JSON path to extract data from the API response.
- **Primary Key**: The unique identifier for records in the resource.
- **Pagination Strategy**: Offset-based pagination with parameters `limit` and `offset`.
- **Error Handling**: Retry logic for handling rate limits and other errors.

# Error Handling

- **Retry Logic**: 
  - Retry on HTTP status code 429 (rate limit).
  - Maximum of 3 retries with exponential backoff (factor of 2).

# Schema

The schema for each resource is defined with neutral field names and types. The primary key for each resource is `id`, and additional fields are included as per the API response structure.