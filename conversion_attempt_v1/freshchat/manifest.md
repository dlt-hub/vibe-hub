# Overview

This document provides a configuration example for integrating with a generic third-party messaging API using the dltHub REST API source format. The API allows for efficient interaction with business users, supporting functionalities such as lead generation, customer engagement, and customer support.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
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
            "name": "roles",
            "endpoint": {
                "path": "/v2/roles",
                "data_selector": "roles",
                "paginator": {
                    "type": "cursor",
                    "limit": 20,
                    "cursor_path": "links.next_page.href"
                }
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/v2/users",
                "data_selector": "users",
                "paginator": {
                    "type": "cursor",
                    "limit": 20,
                    "cursor_path": "links.next_page.href"
                }
            }
        },
        {
            "name": "agents",
            "endpoint": {
                "path": "/v2/agents",
                "data_selector": "agents",
                "paginator": {
                    "type": "cursor",
                    "limit": 20,
                    "cursor_path": "links.next_page.href"
                }
            }
        },
        {
            "name": "groups",
            "endpoint": {
                "path": "/v2/groups",
                "data_selector": "groups",
                "paginator": {
                    "type": "cursor",
                    "limit": 20,
                    "cursor_path": "links.next_page.href"
                }
            }
        },
        {
            "name": "account",
            "endpoint": {
                "path": "/v2/accounts/configuration",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 20,
                    "cursor_path": "links.next_page.href"
                }
            }
        },
        {
            "name": "channels",
            "endpoint": {
                "path": "/v2/channels",
                "data_selector": "channels",
                "paginator": {
                    "type": "cursor",
                    "limit": 20,
                    "cursor_path": "links.next_page.href"
                }
            }
        },
        {
            "name": "conversations",
            "endpoint": {
                "path": "/v2/conversations/{{ stream_partition['conversation_id'] }}",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 20,
                    "cursor_path": "links.next_page.href"
                }
            }
        },
        {
            "name": "users_conversations",
            "endpoint": {
                "path": "/v2/users/{{ stream_partition['user_id'] }}/conversations",
                "data_selector": "conversations",
                "paginator": {
                    "type": "cursor",
                    "limit": 20,
                    "cursor_path": "links.next_page.href"
                }
            }
        },
        {
            "name": "conversations_messages",
            "endpoint": {
                "path": "/v2/conversations/{{ stream_partition['conversation_id'] }}/messages",
                "data_selector": "messages",
                "paginator": {
                    "type": "page_number",
                    "limit": 20,
                    "page_param": "page"
                }
            }
        },
        {
            "name": "conversations_properties",
            "endpoint": {
                "path": "/v2/conversations/fields",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 20,
                    "cursor_path": "links.next_page.href"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Bearer
- **Token**: Retrieved from `dlt.secrets["api_key"]`

## Resources

### Roles
- **Endpoint Path**: `/v2/roles`
- **Data Selector**: `roles`
- **Pagination**: Cursor-based with `links.next_page.href`

### Users
- **Endpoint Path**: `/v2/users`
- **Data Selector**: `users`
- **Pagination**: Cursor-based with `links.next_page.href`

### Agents
- **Endpoint Path**: `/v2/agents`
- **Data Selector**: `agents`
- **Pagination**: Cursor-based with `links.next_page.href`

### Groups
- **Endpoint Path**: `/v2/groups`
- **Data Selector**: `groups`
- **Pagination**: Cursor-based with `links.next_page.href`

### Account
- **Endpoint Path**: `/v2/accounts/configuration`
- **Data Selector**: `data`
- **Pagination**: Cursor-based with `links.next_page.href`

### Channels
- **Endpoint Path**: `/v2/channels`
- **Data Selector**: `channels`
- **Pagination**: Cursor-based with `links.next_page.href`

### Conversations
- **Endpoint Path**: `/v2/conversations/{{ stream_partition['conversation_id'] }}`
- **Data Selector**: `data`
- **Pagination**: Cursor-based with `links.next_page.href`

### Users Conversations
- **Endpoint Path**: `/v2/users/{{ stream_partition['user_id'] }}/conversations`
- **Data Selector**: `conversations`
- **Pagination**: Cursor-based with `links.next_page.href`

### Conversations Messages
- **Endpoint Path**: `/v2/conversations/{{ stream_partition['conversation_id'] }}/messages`
- **Data Selector**: `messages`
- **Pagination**: Page number-based with `page`

### Conversations Properties
- **Endpoint Path**: `/v2/conversations/fields`
- **Data Selector**: `data`
- **Pagination**: Cursor-based with `links.next_page.href`

## Error Handling

- **Retry Logic**: Implement retry on HTTP status codes 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

- **Roles**: Includes fields like `id`, `description`, `name`, `is_custom_role`
- **Users**: Includes fields like `id`, `email`, `phone`, `avatar`, `updated_time`
- **Agents**: Includes fields like `id`, `email`, `avatar`, `groups`, `created_time`
- **Groups**: Includes fields like `id`, `name`, `routing_type`
- **Account**: Includes fields like `account_id`, `app_id`, `plan_type`
- **Channels**: Includes fields like `id`, `icon`, `name`, `updated_time`
- **Conversations**: Includes fields like `conversation_id`, `url`, `status`, `updated_time`
- **Users Conversations**: Includes fields like `id`, `user_id`
- **Conversations Messages**: Includes fields like `id`, `app_id`, `user_id`, `created_time`
- **Conversations Properties**: Includes fields like `id`, `type`, `name`, `label`