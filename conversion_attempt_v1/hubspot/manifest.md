# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various data synchronization types, including incremental and full refresh, and handles authentication, pagination, and error management.

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
            "token_url": "https://api.example.com/oauth/v1/token"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "resource_name",
            "endpoint": {
                "path": "/v1/resource",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "after",
                    "cursor_path": "paging.next.after"
                }
            }
        }
    ]
})
```

# Authentication

The integration supports OAuth2 authentication. The required secrets for authentication include:

- `client_id`: The client ID for the OAuth2 application.
- `client_secret`: The client secret for the OAuth2 application.
- `token_url`: The URL to obtain the OAuth2 token.

# Resources

- **Resource Name**: A generic name for the resource being accessed.
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Cursor-based pagination using the `after` parameter.
- **Data Selector**: The data is extracted from the `data` field in the response.

# Error Handling

The integration includes error handling with retry logic for specific HTTP status codes:

- **Retry on Status Codes**: 429, 500, 502, 503
- **Max Retries**: 3
- **Backoff Factor**: 2

Example error handling configuration:

```python
"endpoint": {
    "path": "/v1/resource",
    "data_selector": "data",
    "error_handler": {
        "retry_on_status_codes": [429, 500, 502, 503],
        "max_retries": 3,
        "backoff_factor": 2
    }
}
```

# Schema

The schema for the data is defined using neutral field names. For example, use `customer_id` instead of `user_id`. The schema should be consistent with the data structure provided by the API and should not include any proprietary or brand-specific terms.