# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various resource endpoints with pagination and authentication mechanisms.

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
            "name": "categories",
            "endpoint": {
                "path": "/v1/resource/categories",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "emoji",
            "endpoint": {
                "path": "/v2/resource/emoji",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "autocomplete_tags",
            "endpoint": {
                "path": "/v1/resource/search/tags",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "channel_search",
            "endpoint": {
                "path": "/v1/resource/channels/search",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "gifs_search",
            "endpoint": {
                "path": "/v1/resource/gifs/search",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "stickers_search",
            "endpoint": {
                "path": "/v1/resource/stickers/search",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: Use `api_key` stored in `dlt.secrets["api_key"]`
- **Location**: Query parameter

## Resources

### Categories
- **Endpoint Path**: `/v1/resource/categories`
- **Pagination Strategy**: Offset with limit 50
- **Data Selector**: `data`

### Emoji
- **Endpoint Path**: `/v2/resource/emoji`
- **Pagination Strategy**: Offset with limit 50
- **Data Selector**: `data`

### Autocomplete Tags
- **Endpoint Path**: `/v1/resource/search/tags`
- **Pagination Strategy**: Offset with limit 50
- **Data Selector**: `data`

### Channel Search
- **Endpoint Path**: `/v1/resource/channels/search`
- **Pagination Strategy**: Offset with limit 50
- **Data Selector**: `data`

### GIFs Search
- **Endpoint Path**: `/v1/resource/gifs/search`
- **Pagination Strategy**: Offset with limit 50
- **Data Selector**: `data`

### Stickers Search
- **Endpoint Path**: `/v1/resource/stickers/search`
- **Pagination Strategy**: Offset with limit 50
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: Implement retry on specific HTTP status codes (e.g., 429, 500)
- **Backoff Strategy**: Exponential backoff with a factor of 2
- **Max Retries**: 3 attempts

## Schema

- **Categories**: Includes fields like `name`, `analytics_id`, and `url`
- **Emoji**: Includes fields like `id`, `import_datetime`, and `url`
- **Autocomplete Tags**: Includes fields like `name`
- **Channel Search**: Includes fields like `id`, `display_name`, and `url`
- **GIFs Search**: Includes fields like `id`, `import_datetime`, and `url`
- **Stickers Search**: Includes fields like `uuid`, `import_datetime`, and `url`

This configuration provides a comprehensive setup for accessing and synchronizing data from a third-party analytics API using dltHub's REST API source.