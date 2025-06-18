# Overview

This document provides a configuration example for integrating with a third-party e-commerce API using the dltHub REST API source. The integration supports various resources such as customers, orders, products, and more, with capabilities for incremental synchronization and pagination.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["api_key"],
            "password": dlt.secrets["api_secret"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge",
        "endpoint": {
            "params": {
                "order": "asc",
                "orderby": "id",
                "dates_are_gmt": "true"
            }
        }
    },
    "resources": [
        {
            "name": "customers",
            "endpoint": {
                "path": "/customers",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset",
                    "limit_param": "per_page"
                }
            }
        },
        {
            "name": "coupons",
            "endpoint": {
                "path": "/coupons",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset",
                    "limit_param": "per_page"
                }
            },
            "incremental_sync": {
                "cursor_field": "date_modified_gmt",
                "start_datetime": dlt.config["start_date"],
                "end_datetime": "now",
                "step": "P30D"
            }
        },
        {
            "name": "orders",
            "endpoint": {
                "path": "/orders",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset",
                    "limit_param": "per_page"
                }
            },
            "incremental_sync": {
                "cursor_field": "date_modified_gmt",
                "start_datetime": dlt.config["start_date"],
                "end_datetime": "now",
                "step": "P30D"
            }
        }
        // Additional resources can be added here following the same pattern
    ]
})
```

# Authentication

- **Type**: Basic Authentication
- **Secrets**: 
  - `api_key`: The API key for authentication.
  - `api_secret`: The API secret for authentication.

# Resources

- **Resource Name**: Abstracted from the original names (e.g., "customers", "orders").
- **Endpoint Path**: Generalized paths (e.g., `/customers`, `/orders`).
- **HTTP Method**: GET (default).
- **Pagination Strategy**: Offset-based pagination with parameters `offset` and `per_page`.
- **Data Selector**: The field path to extract data from the response.

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 and a maximum of 3 retries.

# Schema

- **Fields**: Use neutral field names such as `customer_id`, `order_id`, etc.
- **Types**: Consistent with the YAML schema definitions, using types like `string`, `integer`, `boolean`, etc.
- **Nullability**: Fields can be nullable as indicated in the schema.

This configuration provides a clean, vendor-neutral setup for integrating with a third-party e-commerce API using dltHub's REST API source. It abstracts away any proprietary details and focuses on the reusable patterns for open-source publication or smart assistant training.