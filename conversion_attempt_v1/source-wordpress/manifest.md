# Overview

This document provides a vendor-neutral configuration for integrating with a third-party content management system's REST API. The integration allows for seamless data synchronization of various resources such as users, posts, categories, and more, enabling efficient data analysis and reporting.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/wp-json/",
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
            "name": "users",
            "endpoint": {
                "path": "/v2/users",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "posts",
            "endpoint": {
                "path": "/v2/posts",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "categories",
            "endpoint": {
                "path": "/v2/categories",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "plugins",
            "endpoint": {
                "path": "/v2/plugins",
                "data_selector": "data"
            }
        },
        {
            "name": "editor_blocks",
            "endpoint": {
                "path": "/v2/blocks",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "comments",
            "endpoint": {
                "path": "/v2/comments",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "pages",
            "endpoint": {
                "path": "/v2/pages",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "tags",
            "endpoint": {
                "path": "/v2/tags",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "page_revisions",
            "endpoint": {
                "path": "/v2/pages/{page}/revisions",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "media",
            "endpoint": {
                "path": "/v2/media",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                }
            }
        },
        {
            "name": "taxonomies",
            "endpoint": {
                "path": "/v2/taxonomies",
                "data_selector": "data"
            }
        },
        {
            "name": "types",
            "endpoint": {
                "path": "/v2/types",
                "data_selector": "data"
            }
        },
        {
            "name": "themes",
            "endpoint": {
                "path": "/v2/themes",
                "data_selector": "data"
            }
        },
        {
            "name": "statuses",
            "endpoint": {
                "path": "/v2/statuses",
                "data_selector": "data"
            }
        },
        {
            "name": "settings",
            "endpoint": {
                "path": "/v2/settings",
                "data_selector": "data"
            }
        }
    ]
})
```

## Authentication

- **Type**: Basic Authentication
- **Secrets**: 
  - `username`: The username for basic HTTP authentication.
  - `password`: The password for basic HTTP authentication.

## Resources

- **Resource Name**: Abstracted from the original stream names.
- **Endpoint Path**: Generalized paths for each resource.
- **Pagination Strategy**: Page number-based pagination with parameters `page` and `per_page`.
- **Data Selector**: Default data selector is set to "data".

## Error Handling

- **Retry Logic**: Implement retry logic for common HTTP status codes such as 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 and a maximum of 3 retries.

## Schema

- **Fields**: Generalized field names such as `id`, `name`, `description`, etc.
- **Types**: Consistent with the original schema but generalized for neutrality.
- **Nullability**: Fields can be nullable as per the original schema definitions.