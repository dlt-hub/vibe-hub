# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports multiple resources, each with its own endpoint and pagination strategy. The API requires authentication via a bearer token.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/api/v1",
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
            "name": "features",
            "endpoint": {
                "path": "/features",
                "data_selector": "features",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 5
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
                    "limit_param": "per_page",
                    "limit": 5
                }
            }
        },
        {
            "name": "idea_categories",
            "endpoint": {
                "path": "/products/{id}/idea_categories",
                "data_selector": "idea_categories",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 5
                }
            }
        },
        {
            "name": "ideas",
            "endpoint": {
                "path": "/ideas",
                "data_selector": "ideas",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 5
                }
            }
        },
        {
            "name": "idea_endorsements",
            "endpoint": {
                "path": "/ideas/{id}/endorsements",
                "data_selector": "idea_endorsements",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 5
                }
            }
        },
        {
            "name": "idea_comments",
            "endpoint": {
                "path": "/ideas/{id}/idea_comments",
                "data_selector": "idea_comments",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 5
                }
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/users",
                "data_selector": "users",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 5
                }
            }
        },
        {
            "name": "goals",
            "endpoint": {
                "path": "/goals",
                "data_selector": "goals",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 5
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

Each resource is defined with a specific endpoint path and pagination strategy. The data selector specifies the JSON path to extract data from the API response.

- **features**: `/features`
- **products**: `/products`
- **idea_categories**: `/products/{id}/idea_categories`
- **ideas**: `/ideas`
- **idea_endorsements**: `/ideas/{id}/endorsements`
- **idea_comments**: `/ideas/{id}/idea_comments`
- **users**: `/users`
- **goals**: `/goals`

# Error Handling

The configuration does not specify error handling, but it is recommended to implement retry logic for HTTP status codes like 429, 500, 502, 503, and 504 with exponential backoff.

# Schema

The schema for each resource is defined with neutral field names and types. Fields are nullable and include common data types such as string, boolean, integer, and object.