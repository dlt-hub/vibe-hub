# Overview

This document provides a vendor-neutral configuration for integrating with a third-party invoicing API using dltHub's REST API source. The integration supports data synchronization through RESTful endpoints, allowing for efficient data extraction and management.

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
            "name": "invoices",
            "endpoint": {
                "path": "/v1/invoices",
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
  - `api_key` is stored securely using `dlt.secrets["api_key"]`
  - The API key is included in the query parameters of each request

## Resources

- **Resource Name**: `invoices`
- **Endpoint Path**: `/v1/invoices`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request
- **Data Selector**: The data is extracted from the `data` field in the API response

## Error Handling

- **Retry Logic**: 
  - Retries on HTTP status codes 429, 500, 502, 503, and 504
  - Maximum of 3 retries with an exponential backoff factor of 2

## Schema

- **Fields**: 
  - `id`: Primary key for the resource
  - Additional fields should be defined based on the API's response structure, using neutral field names

This configuration ensures a clean and efficient integration with the invoicing API, abstracting away any vendor-specific details and focusing on the essential parameters required for data synchronization.