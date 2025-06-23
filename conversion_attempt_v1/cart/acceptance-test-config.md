# Overview

This document provides a vendor-neutral configuration for integrating with a third-party e-commerce API using dltHub's REST API source. The integration supports both full refresh and incremental data synchronization, allowing for efficient data extraction from the API.

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
            "name": "order_payments",
            "endpoint": {
                "path": "/v1/order_payments",
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
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Configuration**: The API key is stored securely using `dlt.secrets["api_key"]` and is included in the query parameters of each request.

## Resources

### Order Payments

- **Endpoint Path**: `/v1/order_payments`
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

### Products

- **Endpoint Path**: `/v1/products`
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors. It retries on HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff is applied with a maximum of 3 retries and a backoff factor of 2.

## Schema

- **Fields**: The schema includes fields such as `id`, `customer_id`, and `amount` for the `order_payments` resource, and `product_id`, `name`, and `price` for the `products` resource.
- **Types and Nullability**: All fields are defined with appropriate data types and nullability constraints, ensuring data integrity and consistency.

This configuration provides a robust and flexible setup for extracting data from a third-party e-commerce API, leveraging dltHub's REST API source capabilities.