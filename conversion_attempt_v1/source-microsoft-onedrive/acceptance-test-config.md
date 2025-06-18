# Overview

This document provides a guide for integrating with a third-party file storage API using a RESTful approach. The integration supports various synchronization types, including full refresh and incremental updates, to efficiently manage data retrieval.

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

# Authentication

- **Type**: OAuth2
- **Secrets**: 
  - `client_id`: Your client ID for the API
  - `client_secret`: Your client secret for the API
- **Token URL**: `https://api.example.com/oauth/token`

# Resources

- **Resource Name**: files
- **Endpoint Path**: `/v1/files`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 items per request
- **Data Selector**: `files`

# Error Handling

- **Retry Logic**: 
  - Retry on status codes: 429, 500, 502, 503, 504
  - Maximum retries: 3
  - Backoff factor: 2

# Schema

- **Fields**:
  - `file_id`: Unique identifier for each file
  - `file_name`: Name of the file
  - `created_at`: Timestamp of file creation
  - `modified_at`: Timestamp of last modification
- **Types**: String, DateTime
- **Nullability**: Fields may be nullable depending on the API response

This configuration ensures a robust and flexible integration with the file storage API, allowing for efficient data synchronization and error handling.