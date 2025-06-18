# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports retrieving data from two main resources: tweets and authors. The API uses a bearer token for authentication and supports cursor-based pagination for data retrieval.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v2/tweets",
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
            "name": "tweets",
            "endpoint": {
                "path": "/search/recent",
                "params": {
                    "query": "{{ config['query'] }}",
                    "tweet.fields": "attachments,author_id,context_annotations,conversation_id,created_at,edit_controls,entities,geo,in_reply_to_user_id,lang,possibly_sensitive,referenced_tweets,reply_settings,source,withheld",
                    "max_results": 100
                },
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next_token",
                    "cursor_path": "meta.next_token",
                    "stop_condition": "{{ 'next_token' not in response['meta'] }}"
                }
            }
        },
        {
            "name": "authors",
            "endpoint": {
                "path": "/search/recent",
                "params": {
                    "query": "{{ config['query'] }}",
                    "tweet.fields": "attachments,author_id,context_annotations,conversation_id,created_at,edit_controls,entities,geo,in_reply_to_user_id,lang,possibly_sensitive,referenced_tweets,reply_settings,source,withheld",
                    "user.fields": "created_at,description,entities,id,location,name,pinned_tweet_id,profile_image_url,protected,public_metrics,url,username,verified,verified_type,withheld",
                    "expansions": "author_id",
                    "max_results": 100
                },
                "data_selector": "includes.users",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next_token",
                    "cursor_path": "meta.next_token",
                    "stop_condition": "{{ 'next_token' not in response['meta'] }}"
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

### Tweets

- **Endpoint Path**: `/search/recent`
- **HTTP Method**: GET
- **Primary Key**: `id`
- **Data Selector**: `data`
- **Pagination Strategy**: Cursor-based
  - **Cursor Parameter**: `next_token`
  - **Cursor Path**: `meta.next_token`
  - **Stop Condition**: `{{ 'next_token' not in response['meta'] }}`

### Authors

- **Endpoint Path**: `/search/recent`
- **HTTP Method**: GET
- **Primary Key**: `id`
- **Data Selector**: `includes.users`
- **Pagination Strategy**: Cursor-based
  - **Cursor Parameter**: `next_token`
  - **Cursor Path**: `meta.next_token`
  - **Stop Condition**: `{{ 'next_token' not in response['meta'] }}`

## Error Handling

- **Retry Logic**: Implement retry on specific HTTP status codes with exponential backoff.
- **Known HTTP Response Codes**: 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

### Tweets

- **Fields**:
  - `id`: string
  - `created_at`: string
  - `attachments`: object or null
  - `author_id`: string or null
  - `context_annotations`: array or null
  - `conversation_id`: string or null
  - `edit_controls`: object or null
  - `entities`: object or null
  - `geo`: object or null
  - `in_reply_to_user_id`: string or null
  - `lang`: string or null
  - `possibly_sensitive`: boolean or null
  - `referenced_tweets`: array or null
  - `reply_settings`: string or null
  - `text`: string or null

### Authors

- **Fields**:
  - `id`: string
  - `created_at`: string
  - `description`: string or null
  - `entities`: object or null
  - `location`: string or null
  - `name`: string or null
  - `pinned_tweet_id`: string or null
  - `profile_image_url`: string or null
  - `protected`: boolean or null
  - `public_metrics`: object or null
  - `url`: string or null
  - `username`: string or null
  - `verified`: boolean or null
  - `verified_type`: string or null

This configuration provides a clean and vendor-neutral setup for integrating with a third-party analytics API using dltHub's REST API source format.