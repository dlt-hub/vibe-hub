# Overview

This document provides a configuration example for integrating with a third-party ticketing platform's REST API. The integration allows for data extraction from various resources such as events, orders, tickets, and tags. The API supports pagination and requires API key authentication.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v1/",
        "auth": {
            "type": "api_key",
            "name": "x-api-key",
            "api_key": dlt.secrets["api_key"],
            "location": "header"
        }
    },
    "resource_defaults": {
        "primary_key": "_id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "events",
            "endpoint": {
                "path": "/events",
                "data_selector": "events",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "pageSize",
                    "limit": 100,
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "orders",
            "endpoint": {
                "path": "/events/{eventid}/orders",
                "data_selector": "orders",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "pageSize",
                    "limit": 100,
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "tickets",
            "endpoint": {
                "path": "/events/{eventid}/tickets",
                "data_selector": "tickets",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "pageSize",
                    "limit": 100,
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "tags",
            "endpoint": {
                "path": "/tags",
                "data_selector": "tags",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "pageSize",
                    "limit": 100,
                    "start_from_page": 1
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: API Key
- **Header Name**: `x-api-key`
- **API Key**: Stored securely in `dlt.secrets["api_key"]`

# Resources

### Events
- **Endpoint Path**: `/events`
- **Data Selector**: `events`
- **Pagination**: Page number with parameters `page` and `pageSize`, starting from page 1 with a limit of 100 records per page.

### Orders
- **Endpoint Path**: `/events/{eventid}/orders`
- **Data Selector**: `orders`
- **Pagination**: Page number with parameters `page` and `pageSize`, starting from page 1 with a limit of 100 records per page.

### Tickets
- **Endpoint Path**: `/events/{eventid}/tickets`
- **Data Selector**: `tickets`
- **Pagination**: Page number with parameters `page` and `pageSize`, starting from page 1 with a limit of 100 records per page.

### Tags
- **Endpoint Path**: `/tags`
- **Data Selector**: `tags`
- **Pagination**: Page number with parameters `page` and `pageSize`, starting from page 1 with a limit of 100 records per page.

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

The schema for each resource includes fields such as `_id`, `createdAt`, `updatedAt`, and other relevant fields. All fields are defined with their types and nullability, ensuring consistency with the API's data structure. Use neutral field names like `analytics_id` instead of any branded identifiers.