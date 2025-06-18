# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports data synchronization from various resources, such as forms, responses, webhooks, workspaces, images, and themes. The API uses OAuth2.0 or API key-based authentication to access the resources.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "oauth2",
            "client_id": dlt.secrets["client_id"],
            "client_secret": dlt.secrets["client_secret"],
            "token_url": "https://api.example.com/oauth/token",
            "refresh_token": dlt.secrets["refresh_token"],
            "access_token": dlt.secrets["access_token"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "forms",
            "endpoint": {
                "path": "/v1/forms",
                "data_selector": "items",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "page_size",
                    "limit": 200
                }
            }
        },
        {
            "name": "responses",
            "endpoint": {
                "path": "/v1/forms/{form_id}/responses",
                "data_selector": "items",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "limit_param": "page_size",
                    "limit": 1000
                }
            }
        },
        {
            "name": "webhooks",
            "endpoint": {
                "path": "/v1/forms/{form_id}/webhooks",
                "data_selector": "items"
            }
        },
        {
            "name": "workspaces",
            "endpoint": {
                "path": "/v1/workspaces",
                "data_selector": "items",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "page_size",
                    "limit": 200
                }
            }
        },
        {
            "name": "images",
            "endpoint": {
                "path": "/v1/images",
                "data_selector": "items",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "page_size",
                    "limit": 200
                }
            }
        },
        {
            "name": "themes",
            "endpoint": {
                "path": "/v1/themes",
                "data_selector": "items",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "page_size",
                    "limit": 200
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: OAuth2.0 or API Key
- **Secrets**:
  - `client_id`
  - `client_secret`
  - `refresh_token`
  - `access_token`
- **Token URL**: `https://api.example.com/oauth/token`

# Resources

## Forms
- **Endpoint Path**: `/v1/forms`
- **Pagination**: Page number
- **Data Selector**: `items`

## Responses
- **Endpoint Path**: `/v1/forms/{form_id}/responses`
- **Pagination**: Cursor
- **Data Selector**: `items`

## Webhooks
- **Endpoint Path**: `/v1/forms/{form_id}/webhooks`
- **Data Selector**: `items`

## Workspaces
- **Endpoint Path**: `/v1/workspaces`
- **Pagination**: Page number
- **Data Selector**: `items`

## Images
- **Endpoint Path**: `/v1/images`
- **Pagination**: Page number
- **Data Selector**: `items`

## Themes
- **Endpoint Path**: `/v1/themes`
- **Pagination**: Page number
- **Data Selector**: `items`

# Error Handling

- **Retry Logic**: 
  - Retry on HTTP status codes: 429, 500, 502, 503, 504
  - Maximum retries: 3
  - Backoff factor: 2

# Schema

The schema for each resource is defined with neutral field names and types, ensuring compatibility with the dltHub format. Each resource includes fields such as `id`, `created_at`, `updated_at`, and other relevant attributes, with appropriate data types and descriptions.