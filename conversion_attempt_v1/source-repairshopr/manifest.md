# Overview

This document provides a configuration example for integrating with a generic CRM and marketing platform API using the dltHub REST API source. The integration allows for data extraction from various resources such as customers, invoices, and payments.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v1/",
        "auth": {
            "type": "api_key",
            "name": "Authorization",
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
            "name": "appointment_types",
            "endpoint": {
                "path": "/appointment_types",
                "data_selector": "appointment_types",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "appointments",
            "endpoint": {
                "path": "/appointments",
                "data_selector": "appointments",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "customer_assets",
            "endpoint": {
                "path": "/customer_assets",
                "data_selector": "assets",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "contacts",
            "endpoint": {
                "path": "/contacts",
                "data_selector": "contacts",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "contracts",
            "endpoint": {
                "path": "/contracts",
                "data_selector": "contracts",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "customers",
            "endpoint": {
                "path": "/customers",
                "data_selector": "customers",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "estimates",
            "endpoint": {
                "path": "/estimates",
                "data_selector": "estimates",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "invoices",
            "endpoint": {
                "path": "/invoices",
                "data_selector": "invoices",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "items",
            "endpoint": {
                "path": "/items",
                "data_selector": "items",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "line_items",
            "endpoint": {
                "path": "/line_items",
                "data_selector": "line_items",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "leads",
            "endpoint": {
                "path": "/leads",
                "data_selector": "leads",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "payments",
            "endpoint": {
                "path": "/payments",
                "data_selector": "payments",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "products",
            "endpoint": {
                "path": "/products",
                "data_selector": "products",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "tickets",
            "endpoint": {
                "path": "/tickets",
                "data_selector": "tickets",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: `api_key`
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access your API key.
- **Location**: The API key is included in the request header under the name `Authorization`.

# Resources

Each resource is configured with the following parameters:

- **Resource Name**: A neutral name representing the data being accessed.
- **Endpoint Path**: The path to the resource on the API.
- **Data Selector**: The JSON path to extract data from the API response.
- **Pagination Strategy**: Uses a page number-based pagination with a `page` parameter starting from page 1.

# Error Handling

The configuration does not explicitly define error handling strategies. It is recommended to implement retry logic and handle common HTTP status codes such as 429 (Too Many Requests) and 500-series server errors.

# Schema

The schema for each resource is defined with neutral field names and types. The primary key for all resources is `id`, and fields are nullable where applicable.