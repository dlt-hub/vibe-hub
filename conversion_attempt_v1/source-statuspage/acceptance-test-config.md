# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source. The integration supports data synchronization through a RESTful interface, allowing for efficient data extraction and management.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "api_key",
            "name": "api_key",
            "api_key": dlt.secrets["api_key"],
            "location": "query"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "metrics",
            "endpoint": {
                "path": "/v1/metrics",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "subscribers",
            "endpoint": {
                "path": "/v1/subscribers",
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

- **Type**: `api_key`
- **Secrets**: 
  - `api_key`: The API key used for authenticating requests.
- **Location**: The API key is included in the query parameters of the request.

## Resources

### Metrics

- **Endpoint Path**: `/v1/metrics`
- **HTTP Method**: GET
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the response.

### Subscribers

- **Endpoint Path**: `/v1/subscribers`
- **HTTP Method**: GET
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the response.

## Error Handling

- **Retry Logic**: 
  - Retries are performed on HTTP status codes 429, 500, 502, 503, and 504.
  - A maximum of 3 retries is allowed with an exponential backoff factor of 2.

## Schema

- **Metrics Resource**: 
  - Fields are abstracted and generalized to ensure vendor neutrality.
- **Subscribers Resource**: 
  - Fields are abstracted and generalized to ensure vendor neutrality.

This configuration ensures a clean and efficient integration with the third-party analytics API, following dltHub's REST API source format and terminology.