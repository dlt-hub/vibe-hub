# Overview

This document provides a vendor-neutral configuration for integrating with a third-party analytics API using dltHub's REST API source. The integration supports various data synchronization types, including full refresh and incremental updates, allowing for flexible data retrieval from the API.

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
            "name": "stock_summaries",
            "endpoint": {
                "path": "/v1/stock_summaries",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "customers",
            "endpoint": {
                "path": "/v1/customers",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "items",
            "endpoint": {
                "path": "/v1/items",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "stock_details",
            "endpoint": {
                "path": "/v1/stock_details",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "inventory",
            "endpoint": {
                "path": "/v1/inventory",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "orders",
            "endpoint": {
                "path": "/v1/orders",
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
- **Secrets**: The API key is stored securely using `dlt.secrets["api_key"]`.
- **Location**: The API key is included in the query parameters of the request.

## Resources

- **Resource Names**: `stock_summaries`, `customers`, `items`, `stock_details`, `inventory`, `orders`
- **Endpoint Paths**: Each resource has a unique endpoint path, such as `/v1/stock_summaries`.
- **Pagination Strategy**: Uses an offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry Logic**: The configuration can include retry logic for handling transient errors, such as network issues or rate limiting.
- **HTTP Status Codes**: Common retry status codes include 429 (Too Many Requests), 500 (Internal Server Error), 502 (Bad Gateway), 503 (Service Unavailable), and 504 (Gateway Timeout).
- **Backoff Strategy**: A backoff factor can be applied to manage the retry intervals.

## Schema

- **Fields**: The schema for each resource includes fields such as `id`, `name`, `quantity`, etc.
- **Types and Nullability**: Each field has a defined data type and nullability status, ensuring consistent data handling across resources.