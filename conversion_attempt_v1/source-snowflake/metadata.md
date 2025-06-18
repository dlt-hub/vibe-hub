# Overview

This document provides a vendor-neutral configuration for integrating with a third-party database API using dltHub's REST API source format. The integration supports data synchronization from the database to your data pipeline.

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
            "name": "database_resource",
            "endpoint": {
                "path": "/v1/data",
                "data_selector": "data",
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

- **Resource Name**: `database_resource`
- **Endpoint Path**: `/v1/data`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request
- **Data Selector**: `data`

# Error Handling

- **Retry Logic**: 
  - Retry on HTTP status codes: 429, 500, 502, 503, 504
  - Maximum retries: 3
  - Backoff factor: 2

# Schema

- **Fields**: 
  - `id`: Primary key for the resource
  - Use neutral field names for all other fields

This configuration ensures a clean and efficient integration with a third-party database API, abstracting away any proprietary or branded content.