# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various resources such as calls, company, contacts, numbers, tags, user availability, users, teams, and webhooks. The API supports both full and incremental synchronization based on datetime fields.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v1",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["api_id"],
            "password": dlt.secrets["api_token"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "calls",
            "endpoint": {
                "path": "/calls",
                "data_selector": "calls",
                "paginator": {
                    "type": "page_number",
                    "limit": 50,
                    "page_param": "page",
                    "limit_param": "per_page"
                }
            }
        },
        {
            "name": "company",
            "endpoint": {
                "path": "/company",
                "data_selector": "company",
                "paginator": {
                    "type": "page_number",
                    "limit": 50,
                    "page_param": "page",
                    "limit_param": "per_page"
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
                    "limit": 50,
                    "page_param": "page",
                    "limit_param": "per_page"
                }
            }
        },
        {
            "name": "numbers",
            "endpoint": {
                "path": "/numbers",
                "data_selector": "numbers",
                "paginator": {
                    "type": "page_number",
                    "limit": 50,
                    "page_param": "page",
                    "limit_param": "per_page"
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
                    "limit": 50,
                    "page_param": "page",
                    "limit_param": "per_page"
                }
            }
        },
        {
            "name": "user_availability",
            "endpoint": {
                "path": "/users/availabilities",
                "data_selector": "users"
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/users",
                "data_selector": "users"
            }
        },
        {
            "name": "teams",
            "endpoint": {
                "path": "/teams",
                "data_selector": "teams",
                "paginator": {
                    "type": "page_number",
                    "limit": 50,
                    "page_param": "page",
                    "limit_param": "per_page"
                }
            }
        },
        {
            "name": "webhooks",
            "endpoint": {
                "path": "/webhooks",
                "data_selector": "webhooks",
                "paginator": {
                    "type": "page_number",
                    "limit": 50,
                    "page_param": "page",
                    "limit_param": "per_page"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Basic Authentication
- **Secrets**: 
  - `api_id`: The API ID used for authentication.
  - `api_token`: The API token used for authentication.

# Resources

- **Resource Name**: Abstracted from the original stream names.
- **Endpoint Path**: Generalized paths for each resource.
- **Pagination Strategy**: Page number-based pagination with parameters `page` and `per_page`.
- **Data Selector**: Extracts data from the specified field path in the response.

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 and a maximum of 3 retries.

# Schema

- **Fields**: Generalized field names and types based on the provided schema.
- **Types**: Includes nullability and data types for each field.
- **Primary Key**: Defined for resources where applicable.