# Overview

This document provides a vendor-neutral configuration for integrating with a third-party file storage API using dltHub's REST API source format. The integration supports file transfer capabilities and is designed to work with a generic file-based API.

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
            "token_url": "https://api.example.com/oauth/token"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "file_resource",
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

# Authentication

- **Type**: OAuth2
- **Secrets**: 
  - `client_id`: The client ID for OAuth2 authentication.
  - `client_secret`: The client secret for OAuth2 authentication.
  - `token_url`: The URL to obtain the OAuth2 token.

# Resources

- **Resource Name**: file_resource
- **Endpoint Path**: `/v1/files`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset pagination with a limit of 100 items per request.
- **Data Selector**: `files`

# Error Handling

- **Retry Logic**: 
  - Retry on status codes: 429, 500, 502, 503, 504
  - Maximum retries: 3
  - Backoff factor: 2

# Schema

- **Fields**: 
  - `id`: Primary key for the resource.
  - Use neutral field names consistent with the API's response structure.

This configuration is designed to be flexible and adaptable to various file-based APIs, providing a clean and efficient way to integrate with third-party file storage services using dltHub's REST API source.