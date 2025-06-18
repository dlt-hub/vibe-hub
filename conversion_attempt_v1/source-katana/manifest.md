# Overview

This document provides a configuration example for integrating with a third-party inventory management API using the dltHub REST API source. The API allows for the management of sales channels, products, and materials, ensuring readiness to meet demands. The integration uses OAuth Bearer Token for authentication.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["api_key"]
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
                "path": "/v1/customers",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 50
                }
            }
        },
        {
            "name": "shipping_fee",
            "endpoint": {
                "path": "/v1/sales_order_shipping_fee",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 50
                }
            }
        },
        {
            "name": "costs",
            "endpoint": {
                "path": "/v1/additional_costs",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 50
                }
            }
        },
        {
            "name": "customer_addresses",
            "endpoint": {
                "path": "/v1/customer_addresses",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 50
                }
            }
        },
        {
            "name": "variants",
            "endpoint": {
                "path": "/v1/variants",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 50
                }
            }
        },
        {
            "name": "tax_rates",
            "endpoint": {
                "path": "/v1/tax_rates",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 50
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
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 50
                }
            }
        },
        {
            "name": "stocktakes",
            "endpoint": {
                "path": "/v1/stocktakes",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 50
                }
            }
        },
        {
            "name": "stock_adjustments",
            "endpoint": {
                "path": "/v1/stock_adjustments",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 50
                }
            }
        },
        {
            "name": "sales_orders",
            "endpoint": {
                "path": "/v1/sales_orders",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 50
                }
            }
        },
        {
            "name": "sales_order_fulfillments",
            "endpoint": {
                "path": "/v1/sales_order_fulfillments",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 50
                }
            }
        },
        {
            "name": "sales_order_addresses",
            "endpoint": {
                "path": "/v1/sales_order_addresses",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 50
                }
            }
        },
        {
            "name": "recipes",
            "endpoint": {
                "path": "/v1/recipes",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 50
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
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 50
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
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 50
                }
            }
        },
        {
            "name": "price_lists",
            "endpoint": {
                "path": "/v1/price_lists",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 50
                }
            }
        },
        {
            "name": "inventory",
            "endpoint": {
                "path": "/v1/inventory",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 50
                }
            }
        },
        {
            "name": "locations",
            "endpoint": {
                "path": "/v1/locations",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 50
                }
            }
        },
        {
            "name": "manufacturing_orders",
            "endpoint": {
                "path": "/v1/manufacturing_orders",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 50
                }
            }
        },
        {
            "name": "materials",
            "endpoint": {
                "path": "/v1/materials",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 50
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer
- **Token**: Retrieved from `dlt.secrets["api_key"]`

# Resources

Each resource is configured with the following parameters:

- **Resource Name**: Abstracted from the original stream name
- **Endpoint Path**: The path to the resource, e.g., `/v1/customers`
- **HTTP Method**: GET (default)
- **Data Selector**: `data`
- **Pagination Strategy**: Page number-based pagination with parameters `page` and `limit`, default limit set to 50

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 and a maximum of 3 retries.

# Schema

The schema for each resource is defined with neutral field names and types, ensuring compatibility with the dltHub format. Each schema includes fields such as `id`, `updated_at`, and other relevant attributes, with types specified as `string`, `number`, `boolean`, or `array`, and nullability indicated where applicable.