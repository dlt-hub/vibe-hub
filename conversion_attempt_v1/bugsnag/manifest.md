# Overview

This document provides a configuration example for integrating with a third-party error monitoring and reporting API using the dltHub REST API source. The integration supports various resources such as organizations, projects, saved searches, errors, events, and more. The API allows for data retrieval with pagination and supports authentication via API key.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "api_key",
            "name": "Authorization",
            "api_key": dlt.secrets["auth_token"],
            "location": "header"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge",
        "endpoint": {
            "params": {
                "per_page": 50
            }
        }
    },
    "resources": [
        {
            "name": "organizations",
            "endpoint": {
                "path": "/user/organizations",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 50,
                    "cursor_param": "Link",
                    "stop_condition": "not headers.get('Link') or 'rel=\"next\"' not in headers.get('Link')"
                }
            }
        },
        {
            "name": "projects",
            "endpoint": {
                "path": "/organizations/{{ stream_partition['organization_id'] }}/projects",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 50,
                    "cursor_param": "Link",
                    "stop_condition": "not headers.get('Link') or 'rel=\"next\"' not in headers.get('Link')"
                }
            }
        },
        {
            "name": "saved_searches",
            "endpoint": {
                "path": "/projects/{{ stream_partition['project_id'] }}/saved_searches",
                "data_selector": "data"
            }
        },
        {
            "name": "errors",
            "endpoint": {
                "path": "/projects/{{ stream_partition['project_id'] }}/errors",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 50,
                    "cursor_param": "Link",
                    "stop_condition": "not headers.get('Link') or 'rel=\"next\"' not in headers.get('Link')"
                }
            }
        },
        {
            "name": "events",
            "endpoint": {
                "path": "/projects/{{ stream_partition['project_id'] }}/events",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 50,
                    "cursor_param": "Link",
                    "stop_condition": "not headers.get('Link') or 'rel=\"next\"' not in headers.get('Link')"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: API Key
- **Header Name**: Authorization
- **Secret Key**: `auth_token`

# Resources

### Organizations
- **Endpoint Path**: `/user/organizations`
- **Primary Key**: `id`
- **Pagination**: Cursor-based using the `Link` header

### Projects
- **Endpoint Path**: `/organizations/{{ stream_partition['organization_id'] }}/projects`
- **Primary Key**: `id`
- **Pagination**: Cursor-based using the `Link` header

### Saved Searches
- **Endpoint Path**: `/projects/{{ stream_partition['project_id'] }}/saved_searches`
- **Primary Key**: `id`

### Errors
- **Endpoint Path**: `/projects/{{ stream_partition['project_id'] }}/errors`
- **Primary Key**: `id`
- **Pagination**: Cursor-based using the `Link` header

### Events
- **Endpoint Path**: `/projects/{{ stream_partition['project_id'] }}/events`
- **Primary Key**: `id`
- **Pagination**: Cursor-based using the `Link` header

# Error Handling

- **Retry on Status Codes**: 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Strategy**: Exponential backoff using the `Retry-After` header

# Schema

The schema for each resource is defined with fields such as `id`, `created_at`, `updated_at`, and other relevant attributes. Field names are generalized to ensure vendor neutrality.