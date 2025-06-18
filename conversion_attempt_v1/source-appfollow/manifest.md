# Overview

This document provides a configuration guide for integrating with a third-party analytics API using the dltHub REST API source. The integration supports multiple resources, each corresponding to a specific endpoint of the API. The configuration includes authentication, resource paths, data extraction, and error handling.

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
            "name": "users",
            "endpoint": {
                "path": "/v1/account/users",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "app_collections",
            "endpoint": {
                "path": "/v1/account/apps",
                "data_selector": "apps",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "app_lists",
            "endpoint": {
                "path": "/v1/account/apps/app",
                "data_selector": "apps_app",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "stat_reviews",
            "endpoint": {
                "path": "/v1/reviews/stats",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "ratings",
            "endpoint": {
                "path": "/v1/meta/ratings",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [400],
                    "max_retries": 0,
                    "backoff_factor": 0
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Header Name**: `X-AppFollow-API-Token`
- **API Key**: Stored securely in `dlt.secrets["api_key"]`

## Resources

### Users
- **Endpoint Path**: `/v1/account/users`
- **Data Selector**: `data`
- **Pagination**: Single page

### App Collections
- **Endpoint Path**: `/v1/account/apps`
- **Data Selector**: `apps`
- **Pagination**: Single page

### App Lists
- **Endpoint Path**: `/v1/account/apps/app`
- **Data Selector**: `apps_app`
- **Pagination**: Single page

### Stat Reviews
- **Endpoint Path**: `/v1/reviews/stats`
- **Data Selector**: `data`
- **Pagination**: Single page

### Ratings
- **Endpoint Path**: `/v1/meta/ratings`
- **Data Selector**: `data`
- **Pagination**: Single page
- **Error Handling**: Ignore HTTP 400 responses

## Error Handling

- **Retry Logic**: Configured for specific HTTP status codes
- **Backoff Strategy**: Not applicable for the current configuration
- **Known HTTP Response Codes**: 400 (ignored for ratings resource)

## Schema

### Users
- **Fields**: `email`, `id`, `name`, `role`, `status`, `updated`
- **Types**: String, Integer, Nullable

### App Collections
- **Fields**: `count_apps`, `countries`, `created`, `id`, `languages`, `tags`, `title`, `title_normalized`
- **Types**: String, Integer, Array, Nullable

### App Lists
- **Fields**: `app`, `app_collection_id`, `app_id`, `count_reviews`, `count_whatsnew`, `created`, `ext_id`, `has_reply_integration`, `is_favorite`, `store`, `watch_url`
- **Types**: Object, Integer, String, Nullable

### Stat Reviews
- **Fields**: `ext_id`, `reviews_stat`, `store`
- **Types**: Object, String, Nullable

### Ratings
- **Fields**: `ext_id`, `list`, `store`
- **Types**: Object, Array, String, Nullable

This configuration provides a comprehensive setup for accessing and extracting data from a third-party analytics API using dltHub's REST API source.