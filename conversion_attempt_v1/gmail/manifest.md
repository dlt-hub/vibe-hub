# Overview

This document provides a configuration guide for integrating with a third-party email service API using dltHub's REST API source format. The integration allows for the extraction of various resources such as profiles, drafts, labels, messages, and threads. The API supports OAuth2 authentication and utilizes cursor-based pagination for efficient data retrieval.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v1/users/me/",
        "auth": {
            "type": "oauth2",
            "client_id": dlt.secrets["client_id"],
            "client_secret": dlt.secrets["client_secret"],
            "refresh_token": dlt.secrets["client_refresh_token"],
            "token_url": "https://accounts.example.com/o/oauth2/token"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "profile",
            "endpoint": {
                "path": "/profile",
                "data_selector": "data"
            }
        },
        {
            "name": "drafts",
            "endpoint": {
                "path": "/drafts",
                "data_selector": "drafts",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "pageToken",
                    "stop_condition": "not response.get('nextPageToken', {})"
                }
            }
        },
        {
            "name": "labels",
            "endpoint": {
                "path": "/labels",
                "data_selector": "labels"
            }
        },
        {
            "name": "labels_details",
            "endpoint": {
                "path": "/labels/{label_id}",
                "data_selector": "data"
            }
        },
        {
            "name": "messages",
            "endpoint": {
                "path": "/messages",
                "data_selector": "messages",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "pageToken",
                    "stop_condition": "not response.get('nextPageToken', {})"
                }
            }
        },
        {
            "name": "messages_details",
            "endpoint": {
                "path": "/messages/{message_id}",
                "data_selector": "data"
            }
        },
        {
            "name": "threads",
            "endpoint": {
                "path": "/threads",
                "data_selector": "threads",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "pageToken",
                    "stop_condition": "not response.get('nextPageToken', {})"
                }
            }
        },
        {
            "name": "threads_details",
            "endpoint": {
                "path": "/threads/{thread_id}",
                "data_selector": "data"
            }
        }
    ]
})
```

# Authentication

- **Type**: OAuth2
- **Secrets**:
  - `client_id`: Your OAuth client ID
  - `client_secret`: Your OAuth client secret
  - `client_refresh_token`: Your OAuth refresh token
- **Token URL**: `https://accounts.example.com/o/oauth2/token`

# Resources

- **Profile**
  - **Endpoint Path**: `/profile`
  - **Data Selector**: `data`

- **Drafts**
  - **Endpoint Path**: `/drafts`
  - **Data Selector**: `drafts`
  - **Pagination**: Cursor-based with `pageToken`

- **Labels**
  - **Endpoint Path**: `/labels`
  - **Data Selector**: `labels`

- **Labels Details**
  - **Endpoint Path**: `/labels/{label_id}`
  - **Data Selector**: `data`

- **Messages**
  - **Endpoint Path**: `/messages`
  - **Data Selector**: `messages`
  - **Pagination**: Cursor-based with `pageToken`

- **Messages Details**
  - **Endpoint Path**: `/messages/{message_id}`
  - **Data Selector**: `data`

- **Threads**
  - **Endpoint Path**: `/threads`
  - **Data Selector**: `threads`
  - **Pagination**: Cursor-based with `pageToken`

- **Threads Details**
  - **Endpoint Path**: `/threads/{thread_id}`
  - **Data Selector**: `data`

# Error Handling

- **Retry Logic**: Implement retry on HTTP status codes 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

- **Profile**: Includes fields like `emailAddress`, `historyId`, `messagesTotal`, `threadsTotal`
- **Drafts**: Includes fields like `id`, `message`
- **Labels**: Includes fields like `type`, `id`, `labelListVisibility`, `messageListVisibility`, `name`
- **Messages**: Includes fields like `id`, `threadId`
- **Threads**: Includes fields like `historyId`, `id`, `snippet`

This configuration provides a comprehensive setup for accessing and managing email data through a third-party API, ensuring efficient data synchronization and retrieval.