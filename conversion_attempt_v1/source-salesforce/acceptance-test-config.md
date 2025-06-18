# Overview

This document provides a vendor-neutral configuration for integrating with a third-party analytics API using dltHub's REST API source. The integration supports various data synchronization methods, including full refresh and incremental updates, while ensuring robust error handling and authentication mechanisms.

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
  - `client_id`: The client identifier for the application.
  - `client_secret`: The client secret associated with the client ID.
- **Token URL**: The endpoint to obtain the OAuth2 token.

## Resources

- **Resource Name**: Abstracted resource name
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The field path to extract data from the API response.

## Error Handling

- **Retry Logic**: 
  - Retry on status codes: 429, 500, 502, 503, 504
  - Maximum retries: 3
  - Backoff factor: 2
- **Timeout**: Configurable timeout for API requests to prevent hanging operations.

## Schema

- **Fields**: 
  - `id`: Primary key for deduplication and merging.
  - Other fields are abstracted and should be defined based on the specific API response structure.
- **Types**: Ensure all field types are consistent with the API's data types.
- **Nullability**: Define which fields can be null based on the API's schema.

This configuration provides a clean and generalized setup for integrating with a third-party analytics API using dltHub's REST API source, ensuring flexibility and adaptability to various API structures and authentication methods.