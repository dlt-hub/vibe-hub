# Overview

This document provides a configuration example for integrating with a third-party news API using dltHub's REST API source format. The integration allows for fetching the latest news articles and sources based on specified parameters such as country, language, and category. The API supports pagination and requires an API key for authentication.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "api_key",
            "name": "X-ACCESS-KEY",
            "api_key": dlt.secrets["api_key"],
            "location": "header"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "latest_news",
            "endpoint": {
                "path": "/v1/news",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 10,
                    "cursor_param": "page",
                    "cursor_path": "nextPage"
                }
            }
        },
        {
            "name": "news_sources",
            "endpoint": {
                "path": "/v1/sources",
                "data_selector": "results"
            }
        }
    ]
})
```

# Authentication

- **Type**: API Key
- **Header Name**: `X-ACCESS-KEY`
- **API Key**: Stored securely in `dlt.secrets["api_key"]`

# Resources

## Latest News

- **Endpoint Path**: `/v1/news`
- **HTTP Method**: GET
- **Pagination Strategy**: Cursor-based
  - **Cursor Parameter**: `page`
  - **Cursor Path**: `nextPage`
  - **Limit**: 10
- **Data Selector**: `results`

## News Sources

- **Endpoint Path**: `/v1/sources`
- **HTTP Method**: GET
- **Data Selector**: `results`

# Error Handling

- **Retry Logic**: Implemented for HTTP status codes 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

## Latest News

- **category**: Array of strings, nullable
- **content**: String, nullable
- **country**: Array of strings, nullable
- **creator**: Array of strings, nullable
- **description**: String, nullable
- **image_url**: String, nullable
- **keywords**: Array of strings, nullable
- **language**: String, nullable
- **link**: String, nullable
- **pubDate**: String, nullable
- **source_id**: String, nullable
- **title**: String, nullable
- **video_url**: String, nullable

## News Sources

- **category**: Array of strings, nullable
- **country**: Array of strings, nullable
- **id**: String, nullable
- **language**: Array of strings, nullable
- **name**: String, nullable
- **url**: String, nullable

This configuration provides a clean and vendor-neutral setup for accessing a news API, ensuring compatibility with dltHub's REST API source format.