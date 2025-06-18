# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various resources such as customers, addresses, discounts, and more, with capabilities for incremental synchronization based on datetime fields.

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
                    "type": "cursor",
                    "cursor_param": "next",
                    "stop_condition": "has_more"
                }
            }
        },
        {
            "name": "customer_addresses",
            "endpoint": {
                "path": "/v1/customers/{cus_id}/addresses",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next",
                    "stop_condition": "has_more"
                }
            }
        },
        {
            "name": "discounts",
            "endpoint": {
                "path": "/v1/discounts",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next",
                    "stop_condition": "has_more",
                    "limit": 20
                }
            }
        },
        {
            "name": "prices",
            "endpoint": {
                "path": "/v1/prices",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next",
                    "stop_condition": "has_more",
                    "limit": 20
                }
            }
        },
        {
            "name": "products",
            "endpoint": {
                "path": "/v1/products",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next",
                    "stop_condition": "has_more",
                    "limit": 20
                }
            }
        },
        {
            "name": "businesses",
            "endpoint": {
                "path": "/v1/customers/{cus_id}/businesses",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next",
                    "stop_condition": "has_more"
                }
            }
        },
        {
            "name": "events",
            "endpoint": {
                "path": "/v1/events",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next",
                    "stop_condition": "has_more"
                }
            }
        },
        {
            "name": "event_types",
            "endpoint": {
                "path": "/v1/event-types",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next",
                    "stop_condition": "has_more"
                }
            }
        },
        {
            "name": "reports",
            "endpoint": {
                "path": "/v1/reports",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next",
                    "stop_condition": "has_more"
                }
            }
        },
        {
            "name": "ip_addresses",
            "endpoint": {
                "path": "/v1/ips",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next",
                    "stop_condition": "has_more"
                }
            }
        },
        {
            "name": "subscriptions",
            "endpoint": {
                "path": "/v1/subscriptions",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next",
                    "stop_condition": "has_more"
                }
            }
        },
        {
            "name": "transactions",
            "endpoint": {
                "path": "/v1/transactions",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next",
                    "stop_condition": "has_more"
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

- **Resource Name**: Abstracted from the original YAML
- **Endpoint Path**: Generalized to `/v1/resource_name`
- **Pagination Strategy**: Cursor-based with `cursor_param` as "next" and `stop_condition` as "has_more"
- **Data Selector**: "data"

# Error Handling

- **Retry Logic**: Implement retry on specific HTTP status codes (e.g., 429, 500, 502, 503, 504)
- **Backoff Strategy**: Exponential backoff with a factor of 2
- **Max Retries**: 3 attempts

# Schema

- **Fields**: Generalized field names such as `customer_id`, `updated_at`, etc.
- **Types**: Consistent with the YAML schema, using neutral field names and types.