# Overview

This document provides a vendor-neutral configuration for integrating with a third-party identity management API using dltHub's REST API source format. The integration supports data synchronization through RESTful endpoints, allowing for efficient data retrieval and management.

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
            "token_url": "https://api.example.com/oauth2/token"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "identity_data",
            "endpoint": {
                "path": "/v1/identities",
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
- **Token URL**: `https://api.example.com/oauth2/token`

## Resources

- **Resource Name**: identity_data
- **Endpoint Path**: `/v1/identities`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the response.

## Error Handling

- **Retry Logic**: 
  - Retries on HTTP status codes: 429, 500, 502, 503, 504
  - Maximum retries: 3
  - Backoff factor: 2

## Schema

- **Fields**: 
  - `id`: Unique identifier for each identity record.
  - Additional fields should be defined based on the API's response structure, using neutral field names.

This configuration ensures a clean and efficient integration with the identity management API, following dltHub's REST API source format and terminology.