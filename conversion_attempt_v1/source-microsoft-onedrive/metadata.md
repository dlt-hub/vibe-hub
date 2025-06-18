# Overview

This document provides a configuration guide for integrating with a third-party file storage API using dltHub's REST API source format. The integration supports data synchronization from a file-based source, allowing for efficient data extraction and management.

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
        "primary_key": "file_id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "files",
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
  - `client_id`: Your OAuth2 client ID
  - `client_secret`: Your OAuth2 client secret
- **Token URL**: `https://api.example.com/oauth2/token`

## Resources

- **Resource Name**: files
- **Endpoint Path**: `/v1/files`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 items per request
- **Data Selector**: `files`

## Error Handling

- **Retry Logic**: 
  - Retry on HTTP status codes: 429, 500, 502, 503, 504
  - Maximum retries: 3
  - Backoff factor: 2

## Schema

- **Fields**:
  - `file_id`: Unique identifier for each file
  - `file_name`: Name of the file
  - `file_size`: Size of the file in bytes
  - `created_at`: Timestamp of file creation

This configuration ensures a robust and efficient integration with the file storage API, leveraging dltHub's capabilities for data extraction and synchronization.