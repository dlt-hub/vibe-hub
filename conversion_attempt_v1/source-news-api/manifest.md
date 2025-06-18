# Overview

This document provides a configuration example for integrating with a third-party news API using dltHub's REST API source format. The integration allows for fetching news articles and headlines based on various parameters such as search queries, sources, and date ranges. The API supports pagination and error handling to ensure robust data retrieval.

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
            "location": "header"
        }
    },
    "resource_defaults": {
        "primary_key": "publishedAt",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "news_articles",
            "endpoint": {
                "path": "/v1/articles",
                "data_selector": "articles",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "pageSize"
                }
            }
        },
        {
            "name": "top_headlines",
            "endpoint": {
                "path": "/v1/headlines",
                "data_selector": "articles",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "pageSize"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets**: The API key is stored securely and accessed via `dlt.secrets["api_key"]`.
- **Location**: The API key is included in the request header.

## Resources

### News Articles

- **Resource Name**: `news_articles`
- **Endpoint Path**: `/v1/articles`
- **HTTP Method**: `GET`
- **Pagination Strategy**: Page number-based pagination with parameters `page` and `pageSize`.
- **Data Selector**: `articles`

### Top Headlines

- **Resource Name**: `top_headlines`
- **Endpoint Path**: `/v1/headlines`
- **HTTP Method**: `GET`
- **Pagination Strategy**: Page number-based pagination with parameters `page` and `pageSize`.
- **Data Selector**: `articles`

## Error Handling

- **Retry Logic**: The integration includes retry logic for specific HTTP status codes.
- **Known HTTP Response Codes**: The system will ignore responses with status code 426, indicating too many requests.
- **Fallback Behavior**: If the number of requests exceeds the limit, a message will prompt the user to upgrade their plan.

## Schema

The schema for the resources includes the following fields:

- **author**: Nullable string
- **publishedAt**: Nullable string, formatted as date-time
- **source**: Object containing:
  - **Id**: Nullable string
  - **Name**: Nullable string
- **title**: Nullable string
- **url**: Nullable string

This configuration ensures a clean and efficient integration with the news API, abstracting away any vendor-specific details and focusing on the essential parameters and patterns required for data retrieval.