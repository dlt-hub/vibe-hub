# Overview

This document provides a vendor-neutral configuration for integrating with a third-party fulfillment API using dltHub's REST API source format. The integration supports data synchronization through RESTful endpoints, allowing for efficient data retrieval and management.

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
            "name": "fulfillment_data",
            "endpoint": {
                "path": "/v1/fulfillment",
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
- **Configuration**: The API key is stored securely and accessed via `dlt.secrets["api_key"]`.
- **Location**: The API key is included in the query parameters of each request.

## Resources

- **Resource Name**: `fulfillment_data`
- **Endpoint Path**: `/v1/fulfillment`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors.
- **HTTP Status Codes**: Retries are triggered for status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff is applied with a factor of 2 and a maximum of 3 retries.

## Schema

- **Fields**: The schema includes fields such as `id`, `customer_id`, and `order_status`.
- **Types**: Fields are typed according to their expected data types (e.g., string, integer).
- **Nullability**: Fields are marked as nullable where applicable to accommodate missing data.

This configuration provides a clean and generalized setup for accessing a third-party fulfillment API, ensuring compatibility with dltHub's REST API source format while abstracting away any proprietary or branded content.