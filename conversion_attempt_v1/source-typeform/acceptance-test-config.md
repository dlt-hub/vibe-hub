# Overview

This document provides a vendor-neutral guide for integrating with a third-party analytics API using the dltHub REST API source format. The integration supports various synchronization types, including full refresh and incremental updates, allowing for flexible data retrieval from the API.

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

# Authentication

The integration uses OAuth2 authentication. The following secrets or configuration values are required:

- `client_id`: The client ID for the OAuth2 application.
- `client_secret`: The client secret for the OAuth2 application.
- `token_url`: The URL to obtain the OAuth2 token.

# Resources

- **Resource Name**: A generic name for the resource being accessed.
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data field within the API response that contains the records.

# Error Handling

The integration includes error handling with retry and backoff logic for known HTTP response codes. The configuration includes:

- **Retry on Status Codes**: [429, 500, 502, 503, 504]
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

The schema for the data retrieved from the API is defined with neutral field names. Each field includes its type and nullability. For example:

- `analytics_id`: String, not nullable
- `customer_id`: Integer, nullable

This document ensures that all parameters are sourced from the provided YAML configuration, with all brand-specific terms generalized. The pagination strategies and authentication configurations are mapped to the dltHub format, ensuring no Airbyte-specific language remains.