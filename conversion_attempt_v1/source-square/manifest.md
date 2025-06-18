# Overview

This document provides a configuration example for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports various resources, each with its own endpoint and configuration. The API supports both sandbox and production environments, and authentication can be handled via OAuth or API key.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "oauth2",
            "client_id": dlt.secrets["client_id"],
            "client_secret": dlt.secrets["client_secret"],
            "token_url": "https://api.example.com/oauth2/token"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "customers",
            "endpoint": {
                "path": "/customers",
                "method": "GET",
                "data_selector": "customers",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "stop_condition": "{{ 'cursor' not in response }}"
                },
                "error_handler": {
                    "retry_on_status_codes": [429, 500, 502, 503, 504],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "locations",
            "endpoint": {
                "path": "/locations",
                "method": "GET",
                "data_selector": "locations",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "stop_condition": "{{ 'cursor' not in response }}"
                },
                "error_handler": {
                    "retry_on_status_codes": [429, 500, 502, 503, 504],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "shifts",
            "endpoint": {
                "path": "/labor/shifts/search",
                "method": "POST",
                "data_selector": "shifts",
                "error_handler": {
                    "retry_on_status_codes": [429, 500, 502, 503, 504],
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

The API supports two types of authentication:

- **OAuth2**: Requires `client_id`, `client_secret`, and `token_url`.
- **API Key**: Requires `api_key` and can be included in the request header or query parameters.

# Resources

Each resource is defined with the following parameters:

- **Resource Name**: A unique identifier for the resource.
- **Endpoint Path**: The API endpoint path for the resource.
- **HTTP Method**: The HTTP method to use (GET or POST).
- **Data Selector**: The JSON path to extract data from the response.
- **Pagination Strategy**: Defines how pagination is handled, using cursor-based pagination in this example.
- **Error Handling**: Configures retry logic and backoff strategies for handling API errors.

# Error Handling

The error handling configuration includes:

- **Retry Logic**: Retries requests on specific HTTP status codes (e.g., 429, 500).
- **Backoff Strategy**: Uses exponential backoff with a configurable factor.
- **Max Retries**: Limits the number of retry attempts.

# Schema

The schema for each resource is defined in a neutral format, with fields such as `id`, `created_at`, and `updated_at`. The schema is flexible and can accommodate additional fields as needed.