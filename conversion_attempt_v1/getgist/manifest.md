# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports various resources such as contacts, collections, articles, events, and more, each with its own endpoint and pagination strategy.

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
            "name": "contacts",
            "endpoint": {
                "path": "/contacts",
                "data_selector": "contacts",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 60
                }
            }
        },
        {
            "name": "collections",
            "endpoint": {
                "path": "/collections",
                "data_selector": "collections",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 60
                }
            }
        },
        {
            "name": "articles",
            "endpoint": {
                "path": "/articles",
                "data_selector": "articles",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 60
                }
            }
        },
        {
            "name": "events",
            "endpoint": {
                "path": "/events",
                "data_selector": "events",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 60
                }
            }
        },
        {
            "name": "tags",
            "endpoint": {
                "path": "/tags",
                "data_selector": "tags",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 60
                }
            }
        },
        {
            "name": "segments",
            "endpoint": {
                "path": "/segments",
                "data_selector": "segments",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 60
                }
            }
        },
        {
            "name": "forms",
            "endpoint": {
                "path": "/forms",
                "data_selector": "forms",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 60
                }
            }
        },
        {
            "name": "campaigns",
            "endpoint": {
                "path": "/campaigns",
                "data_selector": "campaigns",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 60
                }
            }
        },
        {
            "name": "subscription_types",
            "endpoint": {
                "path": "/subscription_types",
                "data_selector": "subscription_types",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 60
                }
            }
        },
        {
            "name": "teams",
            "endpoint": {
                "path": "/teams",
                "data_selector": "teams",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 60
                }
            }
        },
        {
            "name": "teammates",
            "endpoint": {
                "path": "/teammates",
                "data_selector": "teammates",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 60
                }
            }
        },
        {
            "name": "stores",
            "endpoint": {
                "path": "/ecommerce/stores",
                "data_selector": "ecommerce_stores",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "page_size",
                    "limit": 60
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

Each resource is configured with a specific endpoint path and pagination strategy. The data selector is used to extract the relevant data from the API response.

- **Resource Name**: Abstracted from the stream name
- **Endpoint Path**: Defined per resource
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Page number-based with parameters `page` and `per_page` or `page_size`
- **Data Selector**: Extracts data from the specified field path

# Error Handling

- **Retry Logic**: Implemented with exponential backoff for HTTP status codes 429, 500, 502, 503, and 504
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

The schema for each resource is defined with neutral field names and types. Required fields are specified, and additional properties are allowed.

- **Fields**: Defined per resource with types and nullability
- **Primary Key**: `id` for all resources
- **Additional Properties**: Allowed for flexibility in data structure