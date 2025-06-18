# Overview

This document provides a configuration example for integrating with a generic third-party analytics API using the dltHub REST API source. The integration supports various data resources, each with its own endpoint and configuration settings. The API uses an API key for authentication and supports pagination and incremental data synchronization.

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
            "name": "users",
            "endpoint": {
                "path": "/users",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page_number",
                    "limit_param": "page_size",
                    "limit": 250
                }
            }
        },
        {
            "name": "orders",
            "endpoint": {
                "path": "/orders",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page_number",
                    "limit_param": "page_size",
                    "limit": 250
                }
            }
        },
        {
            "name": "outlets",
            "endpoint": {
                "path": "/outlets",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page_number",
                    "limit_param": "page_size",
                    "limit": 250
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
                    "page_param": "page_number",
                    "limit_param": "page_size",
                    "limit": 100
                }
            }
        },
        {
            "name": "vouchers",
            "endpoint": {
                "path": "/vouchers",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page_number",
                    "limit_param": "page_size",
                    "limit": 250
                }
            }
        },
        {
            "name": "countries",
            "endpoint": {
                "path": "/countries",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page_number",
                    "limit_param": "page_size",
                    "limit": 250
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
                    "page_param": "page_number",
                    "limit_param": "page_size",
                    "limit": 100
                }
            }
        },
        {
            "name": "inventory",
            "endpoint": {
                "path": "/inventory",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page_number",
                    "limit_param": "page_size",
                    "limit": 250
                }
            }
        },
        {
            "name": "suppliers",
            "endpoint": {
                "path": "/suppliers",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page_number",
                    "limit_param": "page_size",
                    "limit": 250
                }
            }
        },
        {
            "name": "transfers",
            "endpoint": {
                "path": "/transfers",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page_number",
                    "limit_param": "page_size",
                    "limit": 250
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

Each resource is configured with the following parameters:

- **Resource Name**: A generic name representing the data resource.
- **Endpoint Path**: The API endpoint path for the resource.
- **HTTP Method**: Default is `GET`.
- **Data Selector**: The JSON path to extract data from the API response.
- **Pagination Strategy**: Uses `page_number` pagination with parameters for page number and page size.

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504.
- **Max Retries**: Set a maximum number of retries.
- **Backoff Factor**: Use an exponential backoff strategy for retries.

# Schema

- **Fields**: Each resource has a schema with fields, types, and nullability.
- **Field Names**: Use neutral field names such as `customer_id` or `user_key`.

This configuration provides a clean, vendor-neutral setup for integrating with a third-party analytics API using dltHub's REST API source.