# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various resources such as accounts, conversations, users, contacts, and messages, each with its own schema and pagination strategy.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["access_token"]
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
                "path": "/v1/accounts",
                "data_selector": "data.accounts",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next"
                }
            }
        },
        {
            "name": "conversations",
            "endpoint": {
                "path": "/v1/conversations",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next"
                }
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/v1/users",
                "data_selector": "data"
            }
        },
        {
            "name": "contacts",
            "endpoint": {
                "path": "/v1/contacts",
                "data_selector": "data",
                "params": {
                    "email": dlt.config["email"]
                }
            }
        },
        {
            "name": "messages",
            "endpoint": {
                "path": "/v1/conversations/{parent_id}/messages",
                "data_selector": "data.messages",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer
- **Token**: Access token is stored in `dlt.secrets["access_token"]`.

# Resources

## Accounts
- **Endpoint Path**: `/v1/accounts`
- **Primary Key**: `ownerId`
- **Pagination**: Cursor-based using `next` as the cursor parameter.
- **Data Selector**: `data.accounts`

## Conversations
- **Endpoint Path**: `/v1/conversations`
- **Primary Key**: `id`
- **Pagination**: Cursor-based using `next` as the cursor parameter.
- **Data Selector**: `data`

## Users
- **Endpoint Path**: `/v1/users`
- **Primary Key**: `id`
- **Data Selector**: `data`

## Contacts
- **Endpoint Path**: `/v1/contacts`
- **Primary Key**: `id`
- **Data Selector**: `data`
- **Parameters**: Includes an email parameter from `dlt.config["email"]`.

## Messages
- **Endpoint Path**: `/v1/conversations/{parent_id}/messages`
- **Primary Key**: `id`
- **Pagination**: Cursor-based using `next` as the cursor parameter.
- **Data Selector**: `data.messages`

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

Each resource has a defined schema with fields, types, and descriptions. The schemas are consistent with the provided YAML configuration and use neutral field names. For example:

- **Accounts**: Includes fields like `accountId`, `createDateTime`, `customProperties`, etc.
- **Conversations**: Includes fields like `contactId`, `conversationTags`, `createdAt`, etc.
- **Users**: Includes fields like `alias`, `availability`, `avatarUrl`, etc.
- **Contacts**: Includes fields like `attributes`, `createdAt`, `id`, etc.
- **Messages**: Includes fields like `type`, `attributes`, `author`, etc.

This configuration ensures a clean and vendor-neutral integration with the third-party analytics API, following the dltHub REST API source format.