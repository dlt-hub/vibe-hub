# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports multiple resources, each with its own endpoint and pagination strategy. The API requires authentication via an API key, and the data is retrieved using GET requests.

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
            "name": "companies",
            "endpoint": {
                "path": "/v1/companies",
                "data_selector": "data.data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per",
                    "limit": 100
                }
            }
        },
        {
            "name": "invoices",
            "endpoint": {
                "path": "/v1/invoices",
                "data_selector": "data.data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per",
                    "limit": 100
                }
            }
        },
        {
            "name": "locations",
            "endpoint": {
                "path": "/v1/locations",
                "data_selector": "data.data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per",
                    "limit": 100
                }
            }
        },
        {
            "name": "products",
            "endpoint": {
                "path": "/v1/products",
                "data_selector": "data.data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per",
                    "limit": 100
                }
            }
        },
        {
            "name": "shipments",
            "endpoint": {
                "path": "/v1/shipments",
                "data_selector": "data.data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per",
                    "limit": 100
                },
                "params": {
                    "sort": "updated_at",
                    "direction": "asc"
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

- **Resource Name**: companies, invoices, locations, products, shipments
- **Endpoint Path**: `/v1/companies`, `/v1/invoices`, `/v1/locations`, `/v1/products`, `/v1/shipments`
- **HTTP Method**: GET
- **Pagination Strategy**: Page number with parameters `page` and `per`
- **Data Selector**: `data.data`

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

- **Fields**: id, name, etc.
- **Types**: string, integer, boolean, etc.
- **Nullability**: Fields may be nullable depending on the schema definition.