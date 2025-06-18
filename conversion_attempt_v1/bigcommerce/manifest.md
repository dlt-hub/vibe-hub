# Overview

This document provides a configuration example for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports various resources such as customers, orders, transactions, and more, with capabilities for incremental synchronization based on date fields.

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
            "location": "header"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "customers",
            "endpoint": {
                "path": "/v3/customers",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 250
                }
            }
        },
        {
            "name": "orders",
            "endpoint": {
                "path": "/v2/orders",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 250
                }
            }
        },
        {
            "name": "transactions",
            "endpoint": {
                "path": "/v2/orders/{order_id}/transactions",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 250
                }
            }
        },
        {
            "name": "pages",
            "endpoint": {
                "path": "/v3/content/pages",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 250
                }
            }
        },
        {
            "name": "products",
            "endpoint": {
                "path": "/v3/catalog/products",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 250
                }
            }
        },
        {
            "name": "channels",
            "endpoint": {
                "path": "/v3/channels",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 250
                }
            }
        },
        {
            "name": "store",
            "endpoint": {
                "path": "/v2/store",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 250
                }
            }
        },
        {
            "name": "order_products",
            "endpoint": {
                "path": "/v2/orders/{order_id}/products",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 250
                }
            }
        },
        {
            "name": "brands",
            "endpoint": {
                "path": "/v3/catalog/brands",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 250
                }
            }
        },
        {
            "name": "categories",
            "endpoint": {
                "path": "/v3/catalog/categories",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 250
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: 
  - `api_key`: The API key used for authentication, stored securely in dltHub secrets.

# Resources

- **Resource Name**: Abstracted from the original YAML, such as "customers", "orders", etc.
- **Endpoint Path and Method**: Each resource has a specific path, e.g., `/v3/customers`, using the GET method.
- **Pagination Strategy**: Uses `page_number` pagination with parameters `page` and `limit`.
- **Data Selector**: Typically set to `data` to extract the relevant data from the API response.

# Error Handling

- **Retry/Backoff Logic**: Implement retry logic for HTTP status codes like 429, 500, 502, 503, and 504 with a maximum of 3 retries and a backoff factor of 2.
- **Known HTTP Response Codes**: Handle common HTTP errors gracefully.

# Schema

- **Fields, Types, Nullability**: Defined per resource, using neutral field names like `customer_id`, `order_id`, etc.
- **Consistency**: The schema is consistent with the YAML input and uses generalized field names.