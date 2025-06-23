# Overview

This document provides a vendor-neutral guide for integrating with a third-party API using dltHub's REST API source format. The integration supports data synchronization through RESTful endpoints, allowing for efficient data extraction and management.

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
  - `api_key` is stored securely using `dlt.secrets["api_key"]`
  - The key is included in the query parameters of each request

## Resources

- **Resource Name**: `resource_name`
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: `GET` (default)
- **Pagination Strategy**: 
  - Type: `offset`
  - Limit: 100
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: 
  - Retries on status codes: 429, 500, 502, 503, 504
  - Maximum retries: 3
  - Backoff factor: 2

## Schema

- **Fields**: 
  - Use neutral field names such as `customer_id`, `transaction_id`, etc.
- **Types and Nullability**: 
  - Define fields based on the API's response structure, ensuring consistency and neutrality in naming conventions.

This guide ensures a clean and generalized approach to integrating with a third-party API using dltHub's REST API source, abstracting away any proprietary or branded content.