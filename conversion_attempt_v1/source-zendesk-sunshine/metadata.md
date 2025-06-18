# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports data synchronization from a generic API endpoint, abstracting away any vendor-specific details.

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
            "name": "resource_name",
            "endpoint": {
                "path": "/v1/resource",
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
  - `client_id`: Your client ID for the API
  - `client_secret`: Your client secret for the API
- **Token URL**: `https://api.example.com/oauth/token`

## Resources

- **Resource Name**: `resource_name`
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset
  - **Limit**: 100
- **Data Selector**: `data`

## Error Handling

- **Retry/Backoff Logic**: 
  - Retry on status codes: 429, 500, 502, 503, 504
  - Maximum retries: 3
  - Backoff factor: 2

## Schema

- **Fields**: 
  - `id`: Primary key
  - Use neutral field names for data mapping

This configuration provides a clean, vendor-neutral setup for integrating with a third-party analytics API using dltHub's REST API source. All parameters are sourced from the provided YAML, ensuring a consistent and generalized approach.