# Overview

This document provides a configuration example for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports various data resources and utilizes OAuth2.0 and API Key authentication methods. The API offers pagination and incremental sync capabilities to efficiently manage data retrieval.

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
            "refresh_token": dlt.secrets["refresh_token"],
            "token_url": "https://auth.example.com/oauth/token"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "opportunities",
            "endpoint": {
                "path": "/v1/opportunities",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 50,
                    "cursor_param": "offset",
                    "cursor_path": "next",
                    "stop_condition": "hasNext is false"
                }
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/v1/users",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 50,
                    "cursor_param": "offset",
                    "cursor_path": "next",
                    "stop_condition": "hasNext is false"
                }
            }
        }
    ]
})
```

# Authentication

The API supports two types of authentication:

1. **OAuth2.0 Authentication**
   - `client_id`: The client ID for the OAuth2.0 application.
   - `client_secret`: The client secret for the OAuth2.0 application.
   - `refresh_token`: The token used to obtain a new access token.
   - `token_url`: The endpoint for refreshing the access token.

2. **API Key Authentication**
   - `api_key`: The API key for accessing the service.
   - `location`: The location where the API key should be included, such as in the query parameters or headers.

# Resources

- **Opportunities**
  - **Endpoint Path**: `/v1/opportunities`
  - **Pagination**: Cursor-based with `offset` as the cursor parameter.
  - **Data Selector**: `data`

- **Users**
  - **Endpoint Path**: `/v1/users`
  - **Pagination**: Cursor-based with `offset` as the cursor parameter.
  - **Data Selector**: `data`

# Error Handling

The API configuration includes error handling with retry logic for specific HTTP status codes. The retry mechanism is configured to handle transient errors with exponential backoff.

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

The schema for each resource is defined using neutral field names. Below is an example schema for the "opportunities" resource:

```json
{
  "type": "object",
  "$schema": "http://json-schema.org/draft-07/schema#",
  "properties": {
    "id": {
      "type": ["null", "string"]
    },
    "name": {
      "type": ["null", "string"]
    },
    "contact": {
      "type": ["null", "string"]
    },
    "createdAt": {
      "type": ["null", "integer"]
    },
    "updatedAt": {
      "type": ["null", "integer"]
    }
  }
}
```

This configuration ensures a clean and efficient integration with the third-party analytics API, leveraging dltHub's capabilities for data extraction and transformation.