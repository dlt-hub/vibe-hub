# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports data synchronization for resources such as reviews, users, and categories. The API allows for incremental data retrieval based on timestamps and supports various authentication and pagination strategies.

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
            "name": "reviews",
            "endpoint": {
                "path": "/account/{account}/workspace/{workspace}/reviews",
                "data_selector": "conversations",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "lastUpdatedISO",
                    "cursor_path": "lastUpdatedISO"
                }
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/account/{account}/users",
                "data_selector": "users"
            }
        },
        {
            "name": "categories",
            "endpoint": {
                "path": "/account/{account}/workspace/{workspace}/categories",
                "data_selector": "categories"
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer
- **Token**: Retrieved from `dlt.secrets["api_key"]`

# Resources

## Reviews
- **Endpoint Path**: `/account/{account}/workspace/{workspace}/reviews`
- **Data Selector**: `conversations`
- **Pagination Strategy**: Cursor-based using `lastUpdatedISO`

## Users
- **Endpoint Path**: `/account/{account}/users`
- **Data Selector**: `users`
- **Primary Key**: `id`

## Categories
- **Endpoint Path**: `/account/{account}/workspace/{workspace}/categories`
- **Data Selector**: `categories`
- **Primary Key**: `id`

# Error Handling

- **Retry Logic**: Implement retry on HTTP status codes 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

## Reviews
- **Fields**: Includes `comments`, `createdAt`, `externalId`, `lastUpdatedISO`, etc.
- **Types**: String, array, object, boolean, integer, number
- **Nullability**: Fields can be null

## Users
- **Fields**: `email`, `id`, `name`
- **Types**: String
- **Nullability**: Fields can be null

## Categories
- **Fields**: `description`, `archived`, `critical`, `groupId`, `name`, etc.
- **Types**: String, boolean, integer, array
- **Nullability**: Fields can be null

This configuration provides a clean and generalized setup for integrating with a third-party analytics API using dltHub's REST API source format, ensuring a vendor-neutral approach.