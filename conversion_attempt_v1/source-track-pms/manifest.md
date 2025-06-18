# Overview

This document provides a configuration example for integrating with a third-party property management system (PMS) API using the dltHub REST API source. The integration supports various resources such as units, users, folios, and more, with pagination and error handling strategies.

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
                "size": 100
            }
        }
    },
    "resources": [
        {
            "name": "tags",
            "endpoint": {
                "path": "/tags",
                "data_selector": "_embedded.tags",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "size",
                    "limit": 100
                }
            }
        },
        {
            "name": "nodes",
            "endpoint": {
                "path": "/pms/nodes",
                "data_selector": "_embedded.nodes",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "size",
                    "limit": 100
                }
            }
        },
        {
            "name": "roles",
            "endpoint": {
                "path": "/roles",
                "data_selector": "_embedded.roles",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "size",
                    "limit": 100
                }
            }
        },
        {
            "name": "taxes",
            "endpoint": {
                "path": "/pms/taxes",
                "data_selector": "_embedded.taxes",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "size",
                    "limit": 100
                }
            }
        },
        {
            "name": "units",
            "endpoint": {
                "path": "/pms/units",
                "data_selector": "_embedded.units",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "size",
                    "limit": 100
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Basic Authentication
- **Secrets**: 
  - `api_key`: Your API key
  - `api_secret`: Your API secret

# Resources

- **Resource Name**: Abstracted from the original names
- **Endpoint Path**: Generalized paths such as `/tags`, `/pms/nodes`, etc.
- **Pagination Strategy**: Page number-based pagination with parameters `page` and `size`
- **Data Selector**: Extracts data from nested fields like `_embedded.tags`

# Error Handling

- **Retry Logic**: 
  - Retry on status codes: 429, 500, 502, 503, 504
  - Maximum retries: 3
  - Backoff strategy: Constant backoff with a delay of 305 seconds

# Schema

- **Fields**: Generalized field names such as `id`, `name`, `createdAt`, etc.
- **Types**: Includes types like `string`, `number`, `boolean`, and `nullability` where applicable

This configuration provides a clean, vendor-neutral setup for integrating with a third-party property management system API using dltHub's REST API source.