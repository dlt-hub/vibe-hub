# Overview

This document provides a configuration example for integrating with a third-party e-commerce API using dltHub's REST API source. The integration allows for data synchronization from various resources provided by the API, supporting a range of data types and structures.

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
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access your API key.
- **Location**: The API key is included in the query parameters of the request.

## Resources

- **Resource Name**: Abstracted to a neutral name such as `resource_name`.
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: Default is `GET`.
- **Pagination Strategy**: Uses an offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry Logic**: Configured to retry on HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2, up to a maximum of 3 retries.

## Schema

- **Fields**: Use neutral field names such as `customer_id`, `order_id`, etc.
- **Types and Nullability**: Ensure consistency with the API's data structure, using generic types and allowing for null values where applicable.

This configuration provides a clean, vendor-neutral setup for integrating with a third-party e-commerce API using dltHub's REST API source, abstracting away any proprietary or branded content.