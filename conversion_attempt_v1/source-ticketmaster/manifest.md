# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports multiple resources, each with its own endpoint and pagination strategy. The API requires authentication via an API key, and error handling is implemented to manage rate limits and retries.

# Configuration Example

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
            "name": "events",
            "endpoint": {
                "path": "/v1/events",
                "data_selector": "_embedded.events",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "size",
                    "limit": 100
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 2,
                    "backoff_factor": 5
                }
            }
        },
        {
            "name": "attractions",
            "endpoint": {
                "path": "/v1/attractions",
                "data_selector": "_embedded.attractions",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "size",
                    "limit": 100
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 2,
                    "backoff_factor": 5
                }
            }
        },
        {
            "name": "venues",
            "endpoint": {
                "path": "/v1/venues",
                "data_selector": "_embedded.venues",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "size",
                    "limit": 100
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 2,
                    "backoff_factor": 5
                }
            }
        },
        {
            "name": "suggest",
            "endpoint": {
                "path": "/v1/suggest",
                "data_selector": "_embedded.venues",
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 2,
                    "backoff_factor": 5
                }
            }
        },
        {
            "name": "event_images",
            "endpoint": {
                "path": "/v1/events/{event_id}/images",
                "data_selector": "[]",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "size",
                    "limit": 100
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 2,
                    "backoff_factor": 5
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: `api_key`
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access the API key.
- **Location**: The API key is included in the query parameters of each request.

# Resources

### Events
- **Endpoint Path**: `/v1/events`
- **Data Selector**: `_embedded.events`
- **Pagination**: Page number with `page` and `size` parameters, limit of 100 per page.

### Attractions
- **Endpoint Path**: `/v1/attractions`
- **Data Selector**: `_embedded.attractions`
- **Pagination**: Page number with `page` and `size` parameters, limit of 100 per page.

### Venues
- **Endpoint Path**: `/v1/venues`
- **Data Selector**: `_embedded.venues`
- **Pagination**: Page number with `page` and `size` parameters, limit of 100 per page.

### Suggest
- **Endpoint Path**: `/v1/suggest`
- **Data Selector**: `_embedded.venues`

### Event Images
- **Endpoint Path**: `/v1/events/{event_id}/images`
- **Data Selector**: `[]`
- **Pagination**: Page number with `page` and `size` parameters, limit of 100 per page.

# Error Handling

- **Retry Logic**: Retries on HTTP status code 429 (rate limit) with a maximum of 2 retries.
- **Backoff Strategy**: Constant backoff with a 5-second delay between retries.

# Schema

The schema for each resource is defined with neutral field names and types, ensuring compatibility with various data structures. Each resource has a primary key field named `id`.