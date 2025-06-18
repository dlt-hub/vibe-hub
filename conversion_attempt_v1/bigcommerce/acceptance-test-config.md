# Overview

This document provides a vendor-neutral configuration for integrating with a third-party e-commerce API using dltHub's REST API source format. The integration supports various data synchronization types, including full refresh and incremental updates, allowing for efficient data extraction from the API.

# Configuration Example

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
            "name": "orders",
            "endpoint": {
                "path": "/v1/orders",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "transactions",
            "endpoint": {
                "path": "/v1/transactions",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "products",
            "endpoint": {
                "path": "/v1/products",
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
            "name": "channels",
            "endpoint": {
                "path": "/v1/channels",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "order_products",
            "endpoint": {
                "path": "/v1/order_products",
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

# Authentication

- **Type**: `api_key`
- **Secrets**: The API key is stored securely and accessed via `dlt.secrets["api_key"]`.
- **Location**: The API key is included in the query parameters of each request.

# Resources

- **Resource Names**: orders, transactions, products, customers, channels, order_products
- **Endpoint Paths**: Each resource has a corresponding endpoint path, e.g., `/v1/orders`.
- **Pagination Strategy**: Offset-based pagination is used with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

# Error Handling

- **Retry Logic**: The configuration includes retry logic for handling transient errors.
- **HTTP Status Codes**: The system retries on common server error codes such as 429, 500, 502, 503, and 504.
- **Backoff Strategy**: A backoff factor is applied to manage retry intervals.

# Schema

- **Fields**: The schema includes fields such as `id`, `name`, `price`, etc., with neutral naming conventions.
- **Types**: Each field has a defined data type, ensuring consistency across data extractions.
- **Nullability**: Fields are marked as nullable where applicable, based on the API's response structure.

This configuration provides a robust framework for integrating with a third-party e-commerce API, ensuring efficient data extraction and synchronization while maintaining a clean, vendor-neutral approach.