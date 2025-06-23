# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports data synchronization for various resources such as calls, conversations, users, and companies. The API uses cursor-based pagination and requires API key authentication.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v3/a/",
        "auth": {
            "type": "api_key",
            "name": "Authorization",
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
            "name": "calls",
            "endpoint": {
                "path": "/{account_id}/calls.json?",
                "data_selector": "calls",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_value": "{{ headers['link']['next']['url'] }}",
                    "stop_condition": "{{ 'next' not in headers['link'] }}"
                }
            }
        },
        {
            "name": "conversations",
            "endpoint": {
                "path": "/{account_id}/text-messages.json?",
                "data_selector": "conversations",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_value": "{{ headers['link']['next']['url'] }}",
                    "stop_condition": "{{ 'next' not in headers['link'] }}"
                }
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/{account_id}/users.json?",
                "data_selector": "users",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_value": "{{ headers['link']['next']['url'] }}",
                    "stop_condition": "{{ 'next' not in headers['link'] }}"
                }
            }
        },
        {
            "name": "companies",
            "endpoint": {
                "path": "/{account_id}/companies.json?",
                "data_selector": "companies",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_value": "{{ headers['link']['next']['url'] }}",
                    "stop_condition": "{{ 'next' not in headers['link'] }}"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: API Key
- **Header Name**: Authorization
- **API Key Location**: Header
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access the API key.

# Resources

## Calls
- **Endpoint Path**: `/{account_id}/calls.json?`
- **Data Selector**: `calls`
- **Pagination**: Cursor-based with a limit of 100 records per page.

## Conversations
- **Endpoint Path**: `/{account_id}/text-messages.json?`
- **Data Selector**: `conversations`
- **Pagination**: Cursor-based with a limit of 100 records per page.

## Users
- **Endpoint Path**: `/{account_id}/users.json?`
- **Data Selector**: `users`
- **Pagination**: Cursor-based with a limit of 100 records per page.

## Companies
- **Endpoint Path**: `/{account_id}/companies.json?`
- **Data Selector**: `companies`
- **Pagination**: Cursor-based with a limit of 100 records per page.

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

The schema for each resource is defined with neutral field names and types. For example, the `calls` resource includes fields like `id`, `created_at`, `customer_name`, and `call_type`. Each field is defined with its type and nullability.