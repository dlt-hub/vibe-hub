# Overview

This document provides a configuration example for integrating with a third-party feedback API using the dltHub REST API source. The API allows for the collection and management of feedback data through various endpoints, supporting operations such as retrieving responses, workspaces, surveys, contacts, tasks, users, and locations. The integration is designed to facilitate seamless data synchronization and analysis.

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
            "api_key": dlt.secrets["auth_token"],
            "location": "header"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "responses",
            "endpoint": {
                "path": "/responses",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 100
                }
            }
        },
        {
            "name": "workspaces",
            "endpoint": {
                "path": "/workspaces",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 100
                }
            }
        },
        {
            "name": "surveys",
            "endpoint": {
                "path": "/surveys",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 100
                }
            }
        },
        {
            "name": "contacts",
            "endpoint": {
                "path": "/contacts",
                "data_selector": "data.list",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 100
                }
            }
        },
        {
            "name": "tasks",
            "endpoint": {
                "path": "/tasks",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 100
                }
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/users",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 100
                }
            }
        },
        {
            "name": "locations",
            "endpoint": {
                "path": "/locations",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 100
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: `api_key`
- **Secrets**: The API key is stored securely in `dlt.secrets["auth_token"]`.
- **Location**: The API key is included in the request header.

# Resources

Each resource corresponds to a specific endpoint in the API:

- **Responses**: `/responses`
- **Workspaces**: `/workspaces`
- **Surveys**: `/surveys`
- **Contacts**: `/contacts`
- **Tasks**: `/tasks`
- **Users**: `/users`
- **Locations**: `/locations`

All resources use the `GET` method and support pagination using a page number strategy with parameters `page` and `limit`.

# Error Handling

The configuration does not explicitly define error handling logic. However, typical error handling strategies include retrying on specific HTTP status codes (e.g., 429, 500) with exponential backoff.

# Schema

The schema for each resource is defined with neutral field names and types. For example, the `responses` resource includes fields such as `id`, `NPS`, `browser`, and `channel`. Each field is defined with its type and nullability, ensuring compatibility with various data structures.