# Overview

This document provides a configuration example for integrating with a third-party API using the dltHub REST API source. The integration supports various resources, each with its own endpoint and pagination strategy. The API uses OAuth2 for authentication, and the data is retrieved using GET requests. The configuration includes error handling and pagination strategies to ensure efficient data retrieval.

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
            "token_url": "https://api.example.com/v2/oauth2/token"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "conversations",
            "endpoint": {
                "path": "/v2/conversations",
                "data_selector": "_embedded.conversations",
                "paginator": {
                    "type": "cursor",
                    "limit": 50,
                    "cursor_path": "_links.next.href",
                    "stop_condition": "_links.next is none"
                }
            }
        },
        {
            "name": "conversation_threads",
            "endpoint": {
                "path": "/v2/conversations/{{ stream_partition['conversation_id'] }}/threads",
                "data_selector": "_embedded.threads",
                "paginator": {
                    "type": "cursor",
                    "limit": 25,
                    "cursor_path": "_links.next.href",
                    "stop_condition": "_links.next is none"
                }
            }
        },
        {
            "name": "customers",
            "endpoint": {
                "path": "/v2/customers",
                "data_selector": "_embedded.customers",
                "paginator": {
                    "type": "cursor",
                    "limit": 50,
                    "cursor_path": "_links.next.href",
                    "stop_condition": "_links.next is none"
                }
            }
        },
        {
            "name": "inboxes",
            "endpoint": {
                "path": "/v2/mailboxes",
                "data_selector": "_embedded.mailboxes",
                "paginator": {
                    "type": "cursor",
                    "limit": 50,
                    "cursor_path": "_links.next.href",
                    "stop_condition": "_links.next is none"
                }
            }
        },
        {
            "name": "inbox_custom_fields",
            "endpoint": {
                "path": "/v2/mailboxes/{{ stream_partition['inbox_id'] }}/fields",
                "data_selector": "_embedded.fields",
                "paginator": {
                    "type": "cursor",
                    "limit": 50,
                    "cursor_path": "_links.next.href",
                    "stop_condition": "_links.next is none"
                }
            }
        },
        {
            "name": "inbox_folders",
            "endpoint": {
                "path": "/v2/mailboxes/{{ stream_partition['inbox_id'] }}/folders",
                "data_selector": "_embedded.folders",
                "paginator": {
                    "type": "cursor",
                    "limit": 50,
                    "cursor_path": "_links.next.href",
                    "stop_condition": "_links.next is none"
                }
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/v2/users",
                "data_selector": "_embedded.users",
                "paginator": {
                    "type": "cursor",
                    "limit": 50,
                    "cursor_path": "_links.next.href",
                    "stop_condition": "_links.next is none"
                }
            }
        },
        {
            "name": "workflows",
            "endpoint": {
                "path": "/v2/workflows",
                "data_selector": "_embedded.workflows",
                "paginator": {
                    "type": "cursor",
                    "limit": 50,
                    "cursor_path": "_links.next.href",
                    "stop_condition": "_links.next is none"
                }
            }
        },
        {
            "name": "tags",
            "endpoint": {
                "path": "/v2/tags",
                "data_selector": "_embedded.tags",
                "paginator": {
                    "type": "cursor",
                    "limit": 50,
                    "cursor_path": "_links.next.href",
                    "stop_condition": "_links.next is none"
                }
            }
        },
        {
            "name": "teams",
            "endpoint": {
                "path": "/v2/teams",
                "data_selector": "_embedded.teams",
                "paginator": {
                    "type": "cursor",
                    "limit": 50,
                    "cursor_path": "_links.next.href",
                    "stop_condition": "_links.next is none"
                }
            }
        },
        {
            "name": "team_members",
            "endpoint": {
                "path": "/v2/teams/{{ stream_partition['team_id'] }}/members",
                "data_selector": "_embedded.users",
                "paginator": {
                    "type": "cursor",
                    "limit": 50,
                    "cursor_path": "_links.next.href",
                    "stop_condition": "_links.next is none"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: OAuth2
- **Client ID**: `dlt.secrets["client_id"]`
- **Client Secret**: `dlt.secrets["client_secret"]`
- **Token URL**: `https://api.example.com/v2/oauth2/token`

# Resources

### Conversations
- **Endpoint Path**: `/v2/conversations`
- **Data Selector**: `_embedded.conversations`
- **Pagination**: Cursor-based with a limit of 50 items per page

### Conversation Threads
- **Endpoint Path**: `/v2/conversations/{{ stream_partition['conversation_id'] }}/threads`
- **Data Selector**: `_embedded.threads`
- **Pagination**: Cursor-based with a limit of 25 items per page

### Customers
- **Endpoint Path**: `/v2/customers`
- **Data Selector**: `_embedded.customers`
- **Pagination**: Cursor-based with a limit of 50 items per page

### Inboxes
- **Endpoint Path**: `/v2/mailboxes`
- **Data Selector**: `_embedded.mailboxes`
- **Pagination**: Cursor-based with a limit of 50 items per page

### Inbox Custom Fields
- **Endpoint Path**: `/v2/mailboxes/{{ stream_partition['inbox_id'] }}/fields`
- **Data Selector**: `_embedded.fields`
- **Pagination**: Cursor-based with a limit of 50 items per page

### Inbox Folders
- **Endpoint Path**: `/v2/mailboxes/{{ stream_partition['inbox_id'] }}/folders`
- **Data Selector**: `_embedded.folders`
- **Pagination**: Cursor-based with a limit of 50 items per page

### Users
- **Endpoint Path**: `/v2/users`
- **Data Selector**: `_embedded.users`
- **Pagination**: Cursor-based with a limit of 50 items per page

### Workflows
- **Endpoint Path**: `/v2/workflows`
- **Data Selector**: `_embedded.workflows`
- **Pagination**: Cursor-based with a limit of 50 items per page

### Tags
- **Endpoint Path**: `/v2/tags`
- **Data Selector**: `_embedded.tags`
- **Pagination**: Cursor-based with a limit of 50 items per page

### Teams
- **Endpoint Path**: `/v2/teams`
- **Data Selector**: `_embedded.teams`
- **Pagination**: Cursor-based with a limit of 50 items per page

### Team Members
- **Endpoint Path**: `/v2/teams/{{ stream_partition['team_id'] }}/members`
- **Data Selector**: `_embedded.users`
- **Pagination**: Cursor-based with a limit of 50 items per page

# Error Handling

- **Retry Logic**: Implement retry on specific HTTP status codes such as 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

The schema for each resource is defined with fields, types, and nullability. Field names are generalized to ensure neutrality. For example, fields like `id`, `createdAt`, and `updatedAt` are used across resources to maintain consistency.