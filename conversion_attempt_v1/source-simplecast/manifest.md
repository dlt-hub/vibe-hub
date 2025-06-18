# Overview

This document provides a configuration guide for integrating with a third-party podcasting platform's REST API using dltHub. The API allows access to various resources such as podcasts, episodes, analytics, categories, and more. The integration supports data retrieval through RESTful endpoints with pagination and authentication mechanisms.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["api_token"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "podcasts",
            "endpoint": {
                "path": "/podcasts/",
                "data_selector": "collection",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "episodes",
            "endpoint": {
                "path": "/podcasts/{{ stream_partition.podcast_id }}/episodes",
                "data_selector": "collection",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "analytics",
            "endpoint": {
                "path": "/analytics",
                "data_selector": [],
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "categories",
            "endpoint": {
                "path": "/categories",
                "data_selector": "collection",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "distribution_channels",
            "endpoint": {
                "path": "/distribution_channels",
                "data_selector": "collection",
                "paginator": {
                    "type": "offset",
                    "limit": 2,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "timezones",
            "endpoint": {
                "path": "/timezones",
                "data_selector": "collection"
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer Token
- **Token**: Use the `api_token` stored in dlt secrets for authentication.

# Resources

### Podcasts
- **Endpoint Path**: `/podcasts/`
- **Data Selector**: `collection`
- **Pagination**: Offset-based with `offset` parameter

### Episodes
- **Endpoint Path**: `/podcasts/{{ stream_partition.podcast_id }}/episodes`
- **Data Selector**: `collection`
- **Pagination**: Offset-based with `offset` parameter

### Analytics
- **Endpoint Path**: `/analytics`
- **Data Selector**: []
- **Pagination**: Offset-based with `offset` parameter

### Categories
- **Endpoint Path**: `/categories`
- **Data Selector**: `collection`
- **Pagination**: Offset-based with `offset` parameter

### Distribution Channels
- **Endpoint Path**: `/distribution_channels`
- **Data Selector**: `collection`
- **Pagination**: Offset-based with `offset` parameter, limit set to 2

### Timezones
- **Endpoint Path**: `/timezones`
- **Data Selector**: `collection`

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

The schema for each resource is defined with neutral field names and types. For example, the `podcasts` resource includes fields like `id`, `title`, and `status`, with types such as string, boolean, and number. Each resource's schema is designed to accommodate the data structure returned by the API, ensuring compatibility and ease of use.