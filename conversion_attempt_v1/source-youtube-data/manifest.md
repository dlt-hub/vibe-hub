# Overview

This document provides a configuration example for integrating with a third-party video data API. The API allows access to various resources such as videos, channels, comments, and related statistics. This integration is designed to be simple and efficient, focusing on retrieving essential data from the API.

## Configuration Example

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
            "location": "query"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "video_data",
            "endpoint": {
                "path": "/v1/videos",
                "data_selector": "items.*.snippet",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "pageToken",
                    "cursor_path": "nextPageToken"
                }
            }
        },
        {
            "name": "channel_data",
            "endpoint": {
                "path": "/v1/channels",
                "data_selector": "items.*.snippet",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "pageToken",
                    "cursor_path": "nextPageToken"
                }
            }
        },
        {
            "name": "comment_data",
            "endpoint": {
                "path": "/v1/commentThreads",
                "data_selector": "items.*.snippet",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "pageToken",
                    "cursor_path": "nextPageToken"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access your API key.

## Resources

### Video Data
- **Endpoint Path**: `/v1/videos`
- **Data Selector**: `items.*.snippet`
- **Pagination**: Cursor-based using `pageToken` as the cursor parameter.

### Channel Data
- **Endpoint Path**: `/v1/channels`
- **Data Selector**: `items.*.snippet`
- **Pagination**: Cursor-based using `pageToken` as the cursor parameter.

### Comment Data
- **Endpoint Path**: `/v1/commentThreads`
- **Data Selector**: `items.*.snippet`
- **Pagination**: Cursor-based using `pageToken` as the cursor parameter.

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 and a maximum of 3 retries.

## Schema

### Video Data Schema
- **Fields**: Includes `description`, `categoryId`, `channelId`, `channelTitle`, `datetime`, `defaultAudioLanguage`, `defaultLanguage`, `liveBroadcastContent`, `localized`, `publishedAt`, `tags`, `thumbnails`, `title`, `videoId`.
- **Primary Key**: `videoId`

### Channel Data Schema
- **Fields**: Includes `contentDetails`, `etag`, `id`, `kind`, `snippet`, `statistics`.
- **Primary Key**: `id`

### Comment Data Schema
- **Fields**: Includes `canReply`, `channelId`, `isPublic`, `topLevelComment`, `totalReplyCount`, `videoId`.
- **Primary Key**: `id`

This configuration provides a streamlined approach to accessing and managing video-related data from a third-party API, ensuring efficient data retrieval and integration.