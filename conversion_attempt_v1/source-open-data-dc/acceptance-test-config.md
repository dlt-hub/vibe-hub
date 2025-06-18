# Overview

This document provides a configuration guide for integrating with a third-party open data API using dltHub's REST API source. The integration supports data synchronization through a RESTful interface, allowing for efficient data retrieval and management.

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

- **Type**: `api_key`
- **Configuration**: 
  - `api_key` is stored securely using `dlt.secrets["api_key"]`.
  - The API key is included in the query parameters of each request.

## Resources

- **Resource Name**: `resource_name`
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry Logic**: 
  - The integration includes retry logic for handling transient errors.
  - Retries are triggered for HTTP status codes such as 429 (Too Many Requests), 500 (Internal Server Error), 502 (Bad Gateway), 503 (Service Unavailable), and 504 (Gateway Timeout).
  - A maximum of 3 retries is attempted with an exponential backoff factor of 2.

## Schema

- **Fields**: The schema is defined with neutral field names to ensure compatibility and generalization.
- **Types and Nullability**: The schema includes data types and nullability constraints as per the API's response structure.

This configuration provides a clean and generalized setup for integrating with a third-party open data API using dltHub's REST API source, ensuring a vendor-neutral approach to data synchronization.