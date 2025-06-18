# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source format. The integration supports various resources, each with its own endpoint and pagination strategy. The API requires authentication via an API key, and error handling is implemented to manage rate limits and server errors.

# Configuration Example

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
            "name": "user_info",
            "endpoint": {
                "path": "/api/v1/me",
                "data_selector": [],
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "search_results",
            "endpoint": {
                "path": "/search",
                "data_selector": ["data", "children"],
                "params": {
                    "q": "{{ config['query'] }}",
                    "exact": "{{ config['exact'] }}",
                    "limit": "{{ config['limit'] }}",
                    "include_over_18": "{{ config['include_over_18'] }}"
                },
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "cursor_path": "data.after"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "subreddit_search",
            "endpoint": {
                "path": "/subreddits/search",
                "data_selector": ["data", "children"],
                "params": {
                    "q": "{{ config['query'] }}",
                    "exact": "{{ config['exact'] }}",
                    "limit": "{{ config['limit'] }}",
                    "include_over_18": "{{ config['include_over_18'] }}"
                },
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "cursor_path": "data.after"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "message_inbox",
            "endpoint": {
                "path": "/message/inbox",
                "data_selector": ["data"],
                "params": {
                    "q": "{{ config['query'] }}",
                    "exact": "{{ config['exact'] }}",
                    "limit": "5",
                    "include_over_18": "{{ config['include_over_18'] }}"
                },
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "cursor_path": "data.after"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "subreddit_popular",
            "endpoint": {
                "path": "/subreddits/popular",
                "data_selector": ["data", "children"],
                "params": {
                    "q": "{{ config['query'] }}",
                    "exact": "{{ config['exact'] }}",
                    "limit": "{{ config['limit'] }}",
                    "include_over_18": "{{ config['include_over_18'] }}"
                },
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "cursor_path": "data.after"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "subreddit_explore",
            "endpoint": {
                "path": "/{{ stream_partition.subreddit }}",
                "data_selector": ["data", "children"],
                "params": {
                    "exact": "{{ config['exact'] }}",
                    "limit": "{{ config['limit'] }}",
                    "include_over_18": "{{ config['include_over_18'] }}"
                },
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "cursor_path": "data.after"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer
- **Token**: Retrieved from `dlt.secrets["api_key"]`

# Resources

- **User Info**
  - **Endpoint Path**: `/api/v1/me`
  - **Pagination**: Single page
  - **Data Selector**: []

- **Search Results**
  - **Endpoint Path**: `/search`
  - **Pagination**: Cursor-based
  - **Data Selector**: `["data", "children"]`
  - **Parameters**: `q`, `exact`, `limit`, `include_over_18`

- **Subreddit Search**
  - **Endpoint Path**: `/subreddits/search`
  - **Pagination**: Cursor-based
  - **Data Selector**: `["data", "children"]`
  - **Parameters**: `q`, `exact`, `limit`, `include_over_18`

- **Message Inbox**
  - **Endpoint Path**: `/message/inbox`
  - **Pagination**: Cursor-based
  - **Data Selector**: `["data"]`
  - **Parameters**: `q`, `exact`, `limit`, `include_over_18`

- **Subreddit Popular**
  - **Endpoint Path**: `/subreddits/popular`
  - **Pagination**: Cursor-based
  - **Data Selector**: `["data", "children"]`
  - **Parameters**: `q`, `exact`, `limit`, `include_over_18`

- **Subreddit Explore**
  - **Endpoint Path**: `/{{ stream_partition.subreddit }}`
  - **Pagination**: Cursor-based
  - **Data Selector**: `["data", "children"]`
  - **Parameters**: `exact`, `limit`, `include_over_18`

# Error Handling

- **Retry on Status Codes**: [429]
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

- **Fields**: Various fields are available depending on the resource, with types including string, number, boolean, and object.
- **Nullability**: Fields may be nullable, as indicated by the schema.