# Overview

This document provides a configuration example for integrating with a third-party blogging platform API using dltHub's REST API source format. The API allows users to access and manage various resources such as users, blogs, posts, pages, and comments. The integration supports OAuth2 authentication and utilizes cursor-based pagination for data retrieval.

## Configuration Example

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
            "token_url": "https://oauth2.example.com/token",
            "refresh_token": dlt.secrets["client_refresh_token"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "users",
            "endpoint": {
                "path": "/v1/users/self/blogs",
                "data_selector": "items",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "nextPageToken",
                    "stop_condition": "response.get('nextPageToken') is None"
                }
            }
        },
        {
            "name": "blogs",
            "endpoint": {
                "path": "/v1/blogs/{blog_id}/posts",
                "data_selector": "items",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "nextPageToken",
                    "stop_condition": "response.get('nextPageToken') is None"
                }
            }
        },
        {
            "name": "posts",
            "endpoint": {
                "path": "/v1/blogs/{blog_id}/posts/{post_id}",
                "data_selector": [],
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "nextPageToken",
                    "stop_condition": "response.get('nextPageToken') is None"
                }
            }
        },
        {
            "name": "pages",
            "endpoint": {
                "path": "/v1/blogs/{blog_id}/pages",
                "data_selector": "items",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "nextPageToken",
                    "stop_condition": "response.get('nextPageToken') is None"
                }
            }
        },
        {
            "name": "comments",
            "endpoint": {
                "path": "/v1/blogs/{blog_id}/posts/{post_id}/comments",
                "data_selector": "items",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "nextPageToken",
                    "stop_condition": "response.get('nextPageToken') is None"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: OAuth2
- **Secrets**: 
  - `client_id`
  - `client_secret`
  - `client_refresh_token`
- **Token URL**: `https://oauth2.example.com/token`

## Resources

### Users
- **Endpoint Path**: `/v1/users/self/blogs`
- **Data Selector**: `items`
- **Pagination**: Cursor-based using `nextPageToken`

### Blogs
- **Endpoint Path**: `/v1/blogs/{blog_id}/posts`
- **Data Selector**: `items`
- **Pagination**: Cursor-based using `nextPageToken`

### Posts
- **Endpoint Path**: `/v1/blogs/{blog_id}/posts/{post_id}`
- **Data Selector**: `[]`
- **Pagination**: Cursor-based using `nextPageToken`

### Pages
- **Endpoint Path**: `/v1/blogs/{blog_id}/pages`
- **Data Selector**: `items`
- **Pagination**: Cursor-based using `nextPageToken`

### Comments
- **Endpoint Path**: `/v1/blogs/{blog_id}/posts/{post_id}/comments`
- **Data Selector**: `items`
- **Pagination**: Cursor-based using `nextPageToken`

## Error Handling

- **Retry Logic**: Implement retry on HTTP status codes such as 429, 500, 502, 503, 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2.
- **Max Retries**: 3 attempts before failing.

## Schema

### Users
- **Fields**: `id`, `description`, `kind`, `locale`, `name`, `pages`, `posts`, `published`, `selfLink`, `status`, `updated`, `url`
- **Primary Key**: `id`

### Blogs
- **Fields**: `id`, `author`, `blog`, `content`, `etag`, `kind`, `published`, `replies`, `selfLink`, `title`, `updated`, `url`
- **Primary Key**: `id`

### Posts
- **Fields**: `id`, `author`, `blog`, `content`, `etag`, `kind`, `published`, `replies`, `selfLink`, `title`, `updated`, `url`
- **Primary Key**: `id`

### Pages
- **Fields**: `id`, `author`, `blog`, `content`, `etag`, `kind`, `published`, `selfLink`, `title`, `updated`, `url`
- **Primary Key**: `id`

### Comments
- **Fields**: `id`, `author`, `blog`, `content`, `kind`, `post`, `published`, `selfLink`, `updated`
- **Primary Key**: `id`