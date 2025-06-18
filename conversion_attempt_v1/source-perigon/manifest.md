# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source format. The integration supports various resources, each with its own endpoint and data extraction logic. The API allows for incremental synchronization based on datetime fields and supports pagination for efficient data retrieval.

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
            "name": "articles",
            "endpoint": {
                "path": "/v1/articles/all",
                "data_selector": "articles",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "size",
                    "limit": 50
                }
            }
        },
        {
            "name": "stories",
            "endpoint": {
                "path": "/v1/stories/all",
                "data_selector": "results",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "size",
                    "limit": 50
                }
            }
        },
        {
            "name": "journalists",
            "endpoint": {
                "path": "/v1/journalists",
                "data_selector": "results"
            }
        },
        {
            "name": "sources",
            "endpoint": {
                "path": "/v1/sources",
                "data_selector": "results"
            }
        },
        {
            "name": "people",
            "endpoint": {
                "path": "/v1/people/all",
                "data_selector": "results"
            }
        },
        {
            "name": "companies",
            "endpoint": {
                "path": "/v1/companies/all",
                "data_selector": "results"
            }
        },
        {
            "name": "topics",
            "endpoint": {
                "path": "/v1/topics/all",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "size",
                    "limit": 50
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

### Articles
- **Endpoint Path**: `/v1/articles/all`
- **Data Selector**: `articles`
- **Pagination**: Page number with `page` and `size` parameters, limit 50

### Stories
- **Endpoint Path**: `/v1/stories/all`
- **Data Selector**: `results`
- **Pagination**: Page number with `page` and `size` parameters, limit 50

### Journalists
- **Endpoint Path**: `/v1/journalists`
- **Data Selector**: `results`

### Sources
- **Endpoint Path**: `/v1/sources`
- **Data Selector**: `results`

### People
- **Endpoint Path**: `/v1/people/all`
- **Data Selector**: `results`

### Companies
- **Endpoint Path**: `/v1/companies/all`
- **Data Selector**: `results`

### Topics
- **Endpoint Path**: `/v1/topics/all`
- **Data Selector**: `data`
- **Pagination**: Page number with `page` and `size` parameters, limit 50

# Error Handling

- **Retry Logic**: Implement retry on specific HTTP status codes (e.g., 429, 500, 502, 503, 504) with a maximum of 3 retries and a backoff factor of 2.

# Schema

Each resource has a defined schema with fields, types, and nullability. Field names are generalized to maintain neutrality. For example, `articleId` is used as a primary key for the "articles" resource, and `updatedAt` is used for incremental synchronization.