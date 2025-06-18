# Overview

This document provides a configuration guide for integrating with a third-party analytics API using a REST API source. The integration supports data synchronization from the API, allowing for efficient data extraction and management.

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

The integration uses OAuth2 authentication. The following secrets are required:

- `client_id`: The client ID for the OAuth2 application.
- `client_secret`: The client secret for the OAuth2 application.
- `token_url`: The URL to obtain the OAuth2 token.

## Resources

- **Resource Name**: A generic name for the resource being accessed.
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data field within the response that contains the records.

## Error Handling

The integration includes error handling with retry logic for specific HTTP status codes:

- **Retry on Status Codes**: 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

The schema for the data extracted from the API is defined with neutral field names. Each field includes its type and nullability status. The primary key for the resource is `id`.

This configuration provides a clean and vendor-neutral setup for integrating with a third-party analytics API using dltHub's REST API source format.