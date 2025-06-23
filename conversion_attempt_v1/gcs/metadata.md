# Overview

This document provides a configuration example for integrating with a third-party cloud storage API using dltHub's REST API source format. The integration supports file-based data retrieval from a cloud storage service, abstracted from any specific vendor or branded content.

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
            "token_url": "https://api.example.com/oauth/token"
        }
    },
    "resource_defaults": {
        "primary_key": "file_id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "cloud_storage_files",
            "endpoint": {
                "path": "/v1/files",
                "data_selector": "files",
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
  - `client_id`: The client ID for OAuth2 authentication.
  - `client_secret`: The client secret for OAuth2 authentication.
- **Token URL**: `https://api.example.com/oauth/token`

## Resources

- **Resource Name**: cloud_storage_files
- **Endpoint Path**: `/v1/files`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 items per request.
- **Data Selector**: `files`

## Error Handling

- **Retry Logic**: 
  - Retry on status codes: 429, 500, 502, 503, 504
  - Maximum retries: 3
  - Backoff factor: 2

## Schema

- **Fields**:
  - `file_id`: Unique identifier for each file.
  - `file_name`: Name of the file.
  - `file_size`: Size of the file in bytes.
  - `last_modified`: Timestamp of the last modification.

This configuration provides a generalized setup for accessing a cloud storage API, ensuring compatibility with dltHub's REST API source format while abstracting away any vendor-specific details.