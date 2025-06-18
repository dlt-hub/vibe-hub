# Overview

This document provides a configuration example for integrating with a third-party media API using the dltHub REST API source. The API allows for searching and retrieving media content such as photos and videos. The integration supports various resource endpoints with pagination and error handling capabilities.

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
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "photos_search",
            "endpoint": {
                "path": "/v1/search",
                "data_selector": "photos",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 1000
                },
                "error_handler": {
                    "retry_on_status_codes": [500],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "photos_curated",
            "endpoint": {
                "path": "/v1/curated",
                "data_selector": "photos",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 1000
                },
                "error_handler": {
                    "retry_on_status_codes": [500],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "collection_featured",
            "endpoint": {
                "path": "/v1/collections/featured",
                "data_selector": "collections",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 1000
                },
                "error_handler": {
                    "retry_on_status_codes": [500],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "videos_search",
            "endpoint": {
                "path": "/videos/search",
                "data_selector": "videos",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 1000
                },
                "error_handler": {
                    "retry_on_status_codes": [500],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "videos_popular",
            "endpoint": {
                "path": "/videos/popular",
                "data_selector": "videos",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 1000
                },
                "error_handler": {
                    "retry_on_status_codes": [500],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access the API key.
- **Location**: The API key is included in the request header under the `Authorization` field.

## Resources

### photos_search
- **Endpoint Path**: `/v1/search`
- **Pagination**: Page number-based with parameters `page` and `per_page`.
- **Data Selector**: `photos`

### photos_curated
- **Endpoint Path**: `/v1/curated`
- **Pagination**: Page number-based with parameters `page` and `per_page`.
- **Data Selector**: `photos`

### collection_featured
- **Endpoint Path**: `/v1/collections/featured`
- **Pagination**: Page number-based with parameters `page` and `per_page`.
- **Data Selector**: `collections`

### videos_search
- **Endpoint Path**: `/videos/search`
- **Pagination**: Page number-based with parameters `page` and `per_page`.
- **Data Selector**: `videos`

### videos_popular
- **Endpoint Path**: `/videos/popular`
- **Pagination**: Page number-based with parameters `page` and `per_page`.
- **Data Selector**: `videos`

## Error Handling

- **Retry Logic**: The integration retries on HTTP status code `500` with a maximum of 3 retries and a backoff factor of 2.

## Schema

The schema for each resource is defined with neutral field names and types, ensuring compatibility with the dltHub format. Each resource's schema includes fields such as `id`, `url`, `title`, and other relevant attributes, with appropriate data types and nullability settings.