# Overview

This document provides a configuration guide for integrating with a third-party analytics API using the dltHub REST API source. The integration supports data synchronization from various resources, utilizing OAuth2.0 or API key authentication methods. The API offers paginated data retrieval and supports dynamic schema transformations.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v0/",
        "auth": {
            "type": "oauth2",  # or "api_key"
            "client_id": dlt.secrets["client_id"],
            "client_secret": dlt.secrets["client_secret"],
            "refresh_token": dlt.secrets["refresh_token"],
            "token_url": "https://api.example.com/oauth2/v1/token"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "bases",
            "endpoint": {
                "path": "/meta/bases",
                "data_selector": "bases",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "offset",
                    "cursor_path": "offset"
                }
            }
        },
        {
            "name": "tables",
            "endpoint": {
                "path": "/meta/bases/{base_id}/tables",
                "data_selector": "tables",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "offset",
                    "cursor_path": "offset"
                }
            }
        }
    ]
})
```

## Authentication

### OAuth2.0

- **Client ID**: `dlt.secrets["client_id"]`
- **Client Secret**: `dlt.secrets["client_secret"]`
- **Refresh Token**: `dlt.secrets["refresh_token"]`
- **Token URL**: `https://api.example.com/oauth2/v1/token`

### API Key

- **API Key**: `dlt.secrets["api_key"]`
- **Location**: `query`

## Resources

### Bases

- **Endpoint Path**: `/meta/bases`
- **Data Selector**: `bases`
- **Pagination**: Cursor-based with `offset` parameter

### Tables

- **Endpoint Path**: `/meta/bases/{base_id}/tables`
- **Data Selector**: `tables`
- **Pagination**: Cursor-based with `offset` parameter

## Error Handling

- **Retry Logic**: Up to 10 retries with a constant backoff of 30 seconds
- **HTTP Status Codes**: 
  - 403: Permission denied or entity unprocessable
  - 401: Invalid credentials
- **Error Messages**: Configurable based on authentication method

## Schema

- **Fields**: 
  - `id`: string or null
  - `name`: string or null
  - `permissionLevel`: string or null
- **Dynamic Transformations**: 
  - Convert keys to lowercase
  - Replace spaces with underscores
  - Add custom fields for metadata

This configuration provides a robust framework for interacting with a third-party analytics API, ensuring secure authentication, efficient data retrieval, and flexible schema management.