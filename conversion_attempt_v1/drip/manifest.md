# Overview

This document provides a configuration guide for integrating with a third-party analytics API using a REST API source. The integration allows for seamless data synchronization across various resources, optimizing data flows for enhanced analytics and reporting.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v2",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["api_key"],
            "password": dlt.secrets["password"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "accounts",
            "endpoint": {
                "path": "/accounts",
                "data_selector": "accounts",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 100
                }
            }
        },
        {
            "name": "broadcasts",
            "endpoint": {
                "path": "/{account_id}/broadcasts",
                "data_selector": "broadcasts",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 100
                }
            }
        },
        {
            "name": "campaigns",
            "endpoint": {
                "path": "/{account_id}/campaigns",
                "data_selector": "campaigns",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 100
                }
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/user",
                "data_selector": "users",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 100
                }
            }
        },
        {
            "name": "workflows",
            "endpoint": {
                "path": "/{account_id}/workflows",
                "data_selector": "workflows",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 100
                }
            }
        },
        {
            "name": "subscribers",
            "endpoint": {
                "path": "/{account_id}/subscribers",
                "data_selector": "subscribers",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 100
                }
            }
        },
        {
            "name": "custom_fields",
            "endpoint": {
                "path": "/{account_id}/custom_field_identifiers",
                "data_selector": [],
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 100
                }
            }
        },
        {
            "name": "conversions",
            "endpoint": {
                "path": "/{account_id}/goals",
                "data_selector": "goals",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 100
                }
            }
        },
        {
            "name": "events",
            "endpoint": {
                "path": "/{account_id}/event_actions",
                "data_selector": [],
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 100
                }
            }
        },
        {
            "name": "tags",
            "endpoint": {
                "path": "/{account_id}/tags",
                "data_selector": [],
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 100
                }
            }
        },
        {
            "name": "webhooks",
            "endpoint": {
                "path": "/{account_id}/webhooks",
                "data_selector": "webhooks",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 100
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Basic Authentication
- **Secrets**: 
  - `api_key`: Your API key for authentication.
  - `password`: Your password for authentication.

## Resources

### Accounts
- **Endpoint Path**: `/accounts`
- **Primary Key**: `id`
- **Data Selector**: `accounts`
- **Pagination**: Page number with `page` parameter and limit of 100

### Broadcasts
- **Endpoint Path**: `/{account_id}/broadcasts`
- **Primary Key**: `id`
- **Data Selector**: `broadcasts`
- **Pagination**: Page number with `page` parameter and limit of 100

### Campaigns
- **Endpoint Path**: `/{account_id}/campaigns`
- **Primary Key**: `id`
- **Data Selector**: `campaigns`
- **Pagination**: Page number with `page` parameter and limit of 100

### Users
- **Endpoint Path**: `/user`
- **Primary Key**: `email`
- **Data Selector**: `users`
- **Pagination**: Page number with `page` parameter and limit of 100

### Workflows
- **Endpoint Path**: `/{account_id}/workflows`
- **Primary Key**: `id`
- **Data Selector**: `workflows`
- **Pagination**: Page number with `page` parameter and limit of 100

### Subscribers
- **Endpoint Path**: `/{account_id}/subscribers`
- **Primary Key**: `id`
- **Data Selector**: `subscribers`
- **Pagination**: Page number with `page` parameter and limit of 100

### Custom Fields
- **Endpoint Path**: `/{account_id}/custom_field_identifiers`
- **Primary Key**: `custom_field_identifiers`
- **Data Selector**: []
- **Pagination**: Page number with `page` parameter and limit of 100

### Conversions
- **Endpoint Path**: `/{account_id}/goals`
- **Primary Key**: `id`
- **Data Selector**: `goals`
- **Pagination**: Page number with `page` parameter and limit of 100

### Events
- **Endpoint Path**: `/{account_id}/event_actions`
- **Primary Key**: `account_id`
- **Data Selector**: []
- **Pagination**: Page number with `page` parameter and limit of 100

### Tags
- **Endpoint Path**: `/{account_id}/tags`
- **Primary Key**: `tags`
- **Data Selector**: []
- **Pagination**: Page number with `page` parameter and limit of 100

### Webhooks
- **Endpoint Path**: `/{account_id}/webhooks`
- **Primary Key**: `id`
- **Data Selector**: `webhooks`
- **Pagination**: Page number with `page` parameter and limit of 100

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

The schema for each resource is defined with neutral field names and types, ensuring compatibility and flexibility for various data structures. Each resource's schema is designed to accommodate the specific data fields returned by the API, with a focus on maintaining data integrity and consistency.