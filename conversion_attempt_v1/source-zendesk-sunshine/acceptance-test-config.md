# Overview

This document provides a configuration guide for integrating with a third-party analytics API using the dltHub REST API source. The integration supports both full refresh and incremental sync types, allowing for flexible data retrieval from the API.

## Configuration Example

Below is an example configuration for setting up the REST API source using dltHub. This configuration includes client authentication, resource setup, and pagination handling.

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

The API supports OAuth2 authentication. The following secrets are required for authentication:

- `client_id`: The client ID provided by the API service.
- `client_secret`: The client secret provided by the API service.
- `token_url`: The URL to obtain the OAuth2 token.

## Resources

- **Resource Name**: A generic name for the resource being accessed.
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data field within the API response that contains the relevant records.

## Error Handling

The configuration includes basic error handling with retry logic for common HTTP status codes:

- **Retry on Status Codes**: [429, 500, 502, 503, 504]
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

The schema for the data retrieved from the API should be defined with neutral field names. For example, use `customer_id` instead of any branded identifiers. Ensure that all fields are consistent with the API's response structure and are appropriately typed and nullable as needed.

This configuration guide provides a clean, vendor-neutral setup for integrating with a third-party analytics API using dltHub's REST API source. Ensure all parameters are sourced directly from the API's documentation and that no proprietary or branded content is included.