# Overview

This document provides a vendor-neutral configuration for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports data synchronization from the API, abstracting away any proprietary or branded content.

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
            "token_url": "https://oauth2.example.com/token"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "analytics_data",
            "endpoint": {
                "path": "/v1/analytics",
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
  - `client_id`: The client ID for OAuth2 authentication.
  - `client_secret`: The client secret for OAuth2 authentication.
- **Token URL**: `https://oauth2.example.com/token`

## Resources

- **Resource Name**: `analytics_data`
- **Endpoint Path**: `/v1/analytics`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: 
  - Retry on HTTP status codes: 429, 500, 502, 503, 504
  - Maximum retries: 3
  - Backoff factor: 2

## Schema

- **Fields**: 
  - `id`: Primary key for the resource.
  - Use neutral field names such as `analytics_id` instead of any branded identifiers.

This configuration provides a clean and generalized setup for integrating with a third-party analytics API, ensuring compatibility with dltHub's REST API source format while abstracting away any proprietary details.