# Overview

This document provides a vendor-neutral configuration for integrating with a third-party directory API using dltHub's REST API source. The integration supports data synchronization through RESTful endpoints, allowing for efficient data retrieval and management.

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
            "token_url": "https://api.example.com/oauth2/token"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "directory_users",
            "endpoint": {
                "path": "/v1/users",
                "data_selector": "users",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: OAuth2
- **Secrets**: 
  - `client_id`: Your client ID for the API
  - `client_secret`: Your client secret for the API
- **Token URL**: `https://api.example.com/oauth2/token`

## Resources

- **Resource Name**: directory_users
- **Endpoint Path**: `/v1/users`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request
- **Data Selector**: `users`

## Error Handling

- **Retry Logic**: 
  - Retry on status codes: 429, 500, 502, 503, 504
  - Maximum retries: 3
  - Backoff factor: 2

## Schema

- **Fields**: 
  - `id`: Unique identifier for each user
  - `etag`: Entity tag for versioning
  - `lastLoginTime`: Timestamp of the last login
- **Field Types**: 
  - `id`: String, non-nullable
  - `etag`: String, nullable
  - `lastLoginTime`: DateTime, nullable

This configuration ensures a clean and efficient integration with the directory API, abstracting away any proprietary or branded content to maintain a neutral and open-source compatible setup.