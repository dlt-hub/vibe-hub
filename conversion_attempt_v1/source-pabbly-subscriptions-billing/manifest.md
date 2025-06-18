# Overview

This document provides a configuration example for integrating with a third-party subscription management API using the dltHub REST API source. The integration allows for seamless data synchronization of subscription-related data such as customer details, payment transactions, and subscription statuses. This setup facilitates efficient reporting, analysis, and decision-making without manual intervention.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["username"],
            "password": dlt.secrets["password"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "addons",
            "endpoint": {
                "path": "/addons/{{ stream_partition.product_id }}",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "limit"
                },
                "error_handler": {
                    "retry_on_status_codes": [400],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "coupons",
            "endpoint": {
                "path": "/coupon/{{ stream_partition.product_id }}",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "limit"
                },
                "error_handler": {
                    "retry_on_status_codes": [400],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "refunds",
            "endpoint": {
                "path": "/refund/{{ stream_partition.customer_id }}",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "limit"
                },
                "error_handler": {
                    "retry_on_status_codes": [],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "invoices",
            "endpoint": {
                "path": "/invoices",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "licenses",
            "endpoint": {
                "path": "/products/{{ stream_partition.product_id }}/licenses",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "limit"
                },
                "error_handler": {
                    "retry_on_status_codes": [400],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "products",
            "endpoint": {
                "path": "/products",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "limit"
                },
                "error_handler": {
                    "retry_on_status_codes": [400],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "customers",
            "endpoint": {
                "path": "/customers",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "multiplans",
            "endpoint": {
                "path": "/multiplans",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "transactions",
            "endpoint": {
                "path": "/transactions/{{ stream_partition.customer_id }}",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "limit"
                },
                "error_handler": {
                    "retry_on_status_codes": [400],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "subscriptions",
            "endpoint": {
                "path": "/subscriptions",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "payment_methods",
            "endpoint": {
                "path": "/paymentmethods/{{ stream_partition.customer_id }}",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "limit"
                },
                "error_handler": {
                    "retry_on_status_codes": [400],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "payment_gateways",
            "endpoint": {
                "path": "/paymentgateways",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "addon_list_category",
            "endpoint": {
                "path": "/addonlistcategory/{{ stream_partition.product_id }}",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "limit"
                },
                "error_handler": {
                    "retry_on_status_codes": [400],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Basic Authentication
- **Secrets**: 
  - `username`: Your API username
  - `password`: Your API password

# Resources

Each resource represents a different endpoint in the API. The configuration includes the resource name, endpoint path, pagination strategy, and data selector.

- **Resource Name**: Abstracted from the original stream names
- **Endpoint Path**: Generalized paths with placeholders for dynamic segments
- **Pagination Strategy**: Page number-based with parameters for page and limit
- **Data Selector**: Extracts data from the "data" field in the response

# Error Handling

- **Retry Logic**: 
  - Retry on specific HTTP status codes (e.g., 400)
  - Maximum retries: 3
  - Backoff factor: 2

# Schema

The schema for each resource is defined with neutral field names and types. The primary key for each resource is "id". The schema includes fields such as `id`, `status`, `createdAt`, `updatedAt`, and other relevant fields, all generalized and abstracted from the original YAML configuration.