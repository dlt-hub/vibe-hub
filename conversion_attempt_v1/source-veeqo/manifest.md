# Overview

This document provides a configuration example for integrating with a third-party inventory and order management API using the dltHub REST API source. The integration allows for seamless data synchronization of resources such as orders, products, inventory levels, and more, facilitating efficient data workflows and business insights.

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
            "name": "tags",
            "endpoint": {
                "path": "/v1/tags",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 25,
                    "page_param": "page",
                    "limit_param": "page_size"
                }
            }
        },
        {
            "name": "orders",
            "endpoint": {
                "path": "/v1/orders",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 25,
                    "page_param": "page",
                    "limit_param": "page_size"
                }
            }
        },
        {
            "name": "stores",
            "endpoint": {
                "path": "/v1/channels",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 25,
                    "page_param": "page",
                    "limit_param": "page_size"
                }
            }
        },
        {
            "name": "company",
            "endpoint": {
                "path": "/v1/current_company",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 25,
                    "page_param": "page",
                    "limit_param": "page_size"
                }
            }
        },
        {
            "name": "returns",
            "endpoint": {
                "path": "/v1/orders/{sellable_id}/returns",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 25,
                    "page_param": "page",
                    "limit_param": "page_size"
                }
            }
        },
        {
            "name": "products",
            "endpoint": {
                "path": "/v1/products",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 25,
                    "page_param": "page",
                    "limit_param": "page_size"
                }
            }
        },
        {
            "name": "customers",
            "endpoint": {
                "path": "/v1/customers",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 25,
                    "page_param": "page",
                    "limit_param": "page_size"
                }
            }
        },
        {
            "name": "suppliers",
            "endpoint": {
                "path": "/v1/suppliers",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 25,
                    "page_param": "page",
                    "limit_param": "page_size"
                }
            }
        },
        {
            "name": "warehouses",
            "endpoint": {
                "path": "/v1/warehouses",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 25,
                    "page_param": "page",
                    "limit_param": "page_size"
                }
            }
        },
        {
            "name": "purchase_orders",
            "endpoint": {
                "path": "/v1/purchase_orders",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 25,
                    "page_param": "page",
                    "limit_param": "page_size"
                }
            }
        },
        {
            "name": "delivery_methods",
            "endpoint": {
                "path": "/v1/delivery_methods",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 25,
                    "page_param": "page",
                    "limit_param": "page_size"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: `api_key`
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access the API key.
- **Location**: The API key is included in the request header.

# Resources

- **Resource Names**: Abstracted to generic terms such as "tags", "orders", "stores", etc.
- **Endpoint Paths**: Generalized paths like `/v1/tags`, `/v1/orders`, etc.
- **Pagination Strategy**: Uses `page_number` pagination with parameters `page` and `page_size`.
- **Data Selector**: The data is extracted from the `data` field in the response.

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Use exponential backoff with a factor of 2 and a maximum of 3 retries.

# Schema

- **Fields**: Use neutral field names such as `id`, `name`, `created_at`, etc.
- **Types**: Include types like `string`, `number`, `boolean`, and handle nullability.
- **Primary Key**: The primary key for each resource is `id`.