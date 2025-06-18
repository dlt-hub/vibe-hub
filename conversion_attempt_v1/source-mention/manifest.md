# Overview

This document provides a configuration guide for integrating with a third-party analytics API using the dltHub REST API source. The integration allows for data extraction from various endpoints, supporting functionalities such as account management, alerts, mentions, statistics, and more.

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
            "api_key": dlt.secrets["api_key"],
            "location": "header"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "alerts",
            "endpoint": {
                "path": "/v1/accounts/{account_id}/alerts",
                "data_selector": "alerts",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "cursor",
                    "stop_condition": "response._links.more is not defined or response._links.more is none"
                }
            }
        },
        {
            "name": "mentions",
            "endpoint": {
                "path": "/v1/accounts/{account_id}/alerts/{alert_id}/mentions",
                "data_selector": "mentions",
                "paginator": {
                    "type": "cursor",
                    "limit": 1000,
                    "cursor_param": "cursor",
                    "stop_condition": "response._links.more is not defined or response._links.more is none"
                }
            }
        },
        {
            "name": "mention_children",
            "endpoint": {
                "path": "/v1/accounts/{account_id}/alerts/{alert_id}/mentions/{mention_id}/children",
                "data_selector": "children",
                "paginator": {
                    "type": "page_number",
                    "limit": 10
                }
            }
        },
        {
            "name": "account_me",
            "endpoint": {
                "path": "/v1/accounts/me",
                "data_selector": "account"
            }
        },
        {
            "name": "account",
            "endpoint": {
                "path": "/v1/accounts/{account_id}",
                "data_selector": "account"
            }
        },
        {
            "name": "alert_tag",
            "endpoint": {
                "path": "/v1/accounts/{account_id}/alerts/{alert_id}/tags",
                "data_selector": "tags"
            }
        },
        {
            "name": "alert_author",
            "endpoint": {
                "path": "/v1/accounts/{account_id}/alerts/{alert_id}/authors",
                "data_selector": "authors",
                "paginator": {
                    "type": "page_number",
                    "limit": 100
                }
            }
        },
        {
            "name": "alert_tasks",
            "endpoint": {
                "path": "/v1/accounts/{account_id}/alerts/{alert_id}/tasks",
                "data_selector": "tasks",
                "paginator": {
                    "type": "page_number",
                    "limit": 100
                }
            }
        },
        {
            "name": "statistics",
            "endpoint": {
                "path": "/v1/accounts/{account_id}/stats",
                "data_selector": "stats",
                "params": {
                    "alerts[]": "{alert_id}",
                    "interval": "{stats_interval}"
                }
            }
        },
        {
            "name": "mention_tasks",
            "endpoint": {
                "path": "/v1/accounts/{account_id}/alerts/{alert_id}/mentions/{mention_id}",
                "data_selector": "mention"
            }
        }
    ]
})
```

# Authentication

- **Type**: `api_key`
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access your API key.
- **Location**: The API key is included in the request header.

# Resources

- **Resource Name**: Abstracted from branded terms.
- **Endpoint Path**: Generalized paths for accessing resources.
- **Pagination Strategy**: Supports cursor and page number pagination.
- **Data Selector**: Specifies the field path to extract data from the response.

# Error Handling

- **Retry Logic**: Implement retry logic for handling transient errors.
- **HTTP Status Codes**: Handle known HTTP response codes for error management.
- **Backoff Strategy**: Use exponential backoff for retries.

# Schema

- **Fields**: Use neutral field names such as `customer_id` or `user_key`.
- **Types and Nullability**: Consistent with the YAML schema, ensuring data integrity.