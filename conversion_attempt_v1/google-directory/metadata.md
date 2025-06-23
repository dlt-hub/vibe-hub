# Overview

This document provides a configuration guide for integrating with a third-party directory API using dltHub's REST API source. The integration supports data synchronization from the API, allowing for efficient data extraction and management.

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
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "directory_resource",
            "endpoint": {
                "path": "/v1/directory",
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

## Authentication

- **Type**: OAuth2
- **Secrets**: 
  - `client_id`: Your OAuth client ID
  - `client_secret`: Your OAuth client secret
- **Token URL**: `https://api.example.com/oauth/token`

## Resources

- **Resource Name**: directory_resource
- **Endpoint Path**: `/v1/directory`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset pagination with a limit of 100 records per request
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: 
  - Retry on status codes: 429, 500, 502, 503, 504
  - Maximum retries: 3
  - Backoff factor: 2

## Schema

- **Fields**: 
  - `id`: Primary key
  - Other fields should be defined based on the API's response structure, using neutral field names.

This configuration provides a clean and vendor-neutral setup for integrating with a directory API using dltHub's REST API source. Ensure all secrets and configuration values are securely stored and managed.