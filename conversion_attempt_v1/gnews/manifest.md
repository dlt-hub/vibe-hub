# Overview

This document provides a configuration guide for integrating with a third-party news API using dltHub's REST API source format. The integration allows for fetching news articles based on various search parameters and supports incremental synchronization based on publication dates.

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
        "primary_key": "url",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "search_articles",
            "endpoint": {
                "path": "/v1/search",
                "data_selector": "articles",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [429, 403],
                    "max_retries": 3,
                    "backoff_factor": 1
                }
            }
        },
        {
            "name": "top_headlines",
            "endpoint": {
                "path": "/v1/top-headlines",
                "data_selector": "articles",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [429, 403],
                    "max_retries": 3,
                    "backoff_factor": 1
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

### Search Articles

- **Endpoint Path**: `/v1/search`
- **HTTP Method**: GET
- **Pagination Strategy**: Single page (no pagination)
- **Data Selector**: `articles`
- **Primary Key**: `url`

### Top Headlines

- **Endpoint Path**: `/v1/top-headlines`
- **HTTP Method**: GET
- **Pagination Strategy**: Single page (no pagination)
- **Data Selector**: `articles`
- **Primary Key**: `url`

## Error Handling

- **Retry Logic**: 
  - Retry on HTTP status codes 429 and 403.
  - Maximum retries: 3
  - Backoff factor: 1 second

## Schema

The schema for both resources includes the following fields:

- **title**: The main title of the article. (nullable)
- **description**: A small paragraph under the title. (nullable)
- **content**: The full content of the article. (nullable)
- **url**: The URL of the article. (nullable)
- **image**: The main image of the article. (nullable)
- **publishedAt**: The publication date of the article in UTC. (nullable)
- **source**: An object containing:
  - **name**: The name of the source. (nullable)
  - **url**: The homepage of the source. (nullable)

## Incremental Sync

- **Type**: Datetime-based cursor
- **Cursor Field**: `publishedAt`
- **Start Date**: Configurable via `start_date` parameter
- **End Date**: Configurable via `end_date` parameter
- **Step**: Weekly (`P1W`)
- **Datetime Format**: `YYYY-MM-DDTHH:MM:SSZ`

This configuration allows for efficient data retrieval and synchronization with the news API, ensuring that only new or updated articles are fetched during each sync operation.