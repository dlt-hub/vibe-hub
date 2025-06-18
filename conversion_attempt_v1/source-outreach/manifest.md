# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source format. The integration supports various resources, each with its own endpoint and pagination strategy. The API uses OAuth2 for authentication and supports incremental synchronization based on a datetime cursor.

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
            "token_url": "https://api.example.com/oauth/token",
            "grant_type": "refresh_token"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "accounts",
            "endpoint": {
                "path": "/v1/accounts",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "links.next",
                    "stop_condition": "not links.next"
                }
            }
        },
        {
            "name": "calls",
            "endpoint": {
                "path": "/v1/calls",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "links.next",
                    "stop_condition": "not links.next"
                }
            }
        },
        // Additional resources follow the same pattern
    ]
})
```

# Authentication

The API uses OAuth2 authentication. The following secrets are required:

- `client_id`: The client ID of your application.
- `client_secret`: The client secret of your application.
- `token_url`: The URL to obtain the access token.
- `grant_type`: The grant type used for token refresh, typically "refresh_token".

# Resources

Each resource is defined with the following parameters:

- **Resource Name**: A neutral name for the resource (e.g., "accounts", "calls").
- **Endpoint Path**: The path to the resource endpoint (e.g., "/v1/accounts").
- **Pagination Strategy**: Uses cursor-based pagination with a cursor path and stop condition.
- **Data Selector**: Specifies the field path to extract data from the response.

# Error Handling

The configuration includes retry logic for handling errors:

- **Retry on Status Codes**: [429, 500, 502, 503, 504]
- **Max Retries**: 3
- **Backoff Factor**: 2

Example error handling configuration:

```python
"endpoint": {
    "path": "/v1/resource",
    "data_selector": "data",
    "error_handler": {
        "retry_on_status_codes": [429, 500, 502, 503, 504],
        "max_retries": 3,
        "backoff_factor": 2
    }
}
```

# Schema

The schema for each resource is defined with neutral field names and types. For example:

- **Primary Key**: "id"
- **Datetime Fields**: Use the format "YYYY-MM-DDTHH:MM:SSZ"
- **String Fields**: Represented as strings
- **Integer Fields**: Represented as integers

This configuration ensures a clean, vendor-neutral integration with the third-party analytics API, following dltHub's REST API source format.