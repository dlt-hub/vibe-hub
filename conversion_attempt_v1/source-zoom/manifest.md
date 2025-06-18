# Overview

This document provides a configuration example for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports various resources, each with its own endpoint and pagination strategy. The API uses OAuth2 authentication for secure access.

## Configuration Example

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
            "token_url": dlt.secrets["authorization_endpoint"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "users",
            "endpoint": {
                "path": "/v1/users",
                "data_selector": "users",
                "paginator": {
                    "type": "cursor",
                    "limit": 30,
                    "cursor_param": "next_page_token"
                }
            }
        },
        {
            "name": "meetings",
            "endpoint": {
                "path": "/v1/meetings/{parent_id}",
                "data_selector": "meetings",
                "paginator": {
                    "type": "single_page"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: OAuth2
- **Secrets**:
  - `client_id`: Your API client ID
  - `client_secret`: Your API client secret
  - `authorization_endpoint`: The token URL for obtaining access tokens

## Resources

### Users
- **Endpoint Path**: `/v1/users`
- **Pagination**: Cursor-based
  - **Cursor Parameter**: `next_page_token`
  - **Limit**: 30
- **Data Selector**: `users`

### Meetings
- **Endpoint Path**: `/v1/meetings/{parent_id}`
- **Pagination**: Single page
- **Data Selector**: `meetings`

## Error Handling

- **Retry Logic**: 
  - Retry on HTTP status codes: 429, 500, 502, 503, 504
  - Maximum retries: 3
  - Backoff factor: 2

## Schema

### Users
- **Fields**:
  - `id`: string
  - `email`: string
  - `first_name`: string
  - `last_name`: string
  - `status`: string
  - `created_at`: string

### Meetings
- **Fields**:
  - `id`: number
  - `topic`: string
  - `start_time`: string
  - `duration`: number
  - `status`: string

This configuration provides a clean, vendor-neutral setup for accessing a third-party analytics API using dltHub's REST API source format. It includes authentication, resource configuration, pagination, error handling, and schema definitions.