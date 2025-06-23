# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source format. The integration supports various resources, each with its own endpoint and configuration settings. The API uses a basic authentication method and supports incremental synchronization based on datetime cursors.

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
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "account",
            "endpoint": {
                "path": "/v1/account",
                "data_selector": [],
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
            "name": "customers",
            "endpoint": {
                "path": "/v1/customers",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 50,
                    "cursor_param": "cursor",
                    "cursor_path": "meta.next_cursor"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "custom_fields",
            "endpoint": {
                "path": "/v1/custom-fields",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 50,
                    "cursor_param": "cursor",
                    "cursor_path": "meta.next_cursor"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        }
        # Additional resources can be added here following the same pattern
    ]
})
```

# Authentication

- **Type**: Basic
- **Secrets**: 
  - `username`: Your API username
  - `password`: Your API password

# Resources

Each resource is configured with the following parameters:

- **Resource Name**: A neutral name representing the data being accessed.
- **Endpoint Path**: The API path to access the resource.
- **HTTP Method**: Default is GET.
- **Data Selector**: The JSON path to extract data from the response.
- **Primary Key**: The unique identifier for records.
- **Write Disposition**: Default is "merge".
- **Pagination Strategy**: Supports cursor-based pagination with a limit of 50 records per page.
- **Error Handling**: Includes retry logic for rate-limited responses.

# Error Handling

- **Retry Logic**: 
  - Retries on HTTP status code 429 (rate limited).
  - Maximum of 3 retries with an exponential backoff factor of 2.
- **Known HTTP Response Codes**: 429 for rate limiting.

# Schema

The schema for each resource is defined with fields, types, and nullability. Field names are generalized to maintain neutrality. For example, `customer_id` is used instead of any branded identifier.