# Overview

This document provides a configuration guide for integrating with a third-party media content API using dltHub's REST API source format. The API allows access to various media resources such as images, videos, audio, and user details. The integration supports data retrieval through RESTful endpoints with pagination and authentication mechanisms.

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
            "name": "media_categories",
            "endpoint": {
                "path": "/v2/media/categories",
                "data_selector": "data"
            }
        },
        {
            "name": "media_search",
            "endpoint": {
                "path": "/v2/media/search",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 20
                }
            }
        },
        {
            "name": "user_details",
            "endpoint": {
                "path": "/v2/user",
                "data_selector": "data"
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer
- **Token**: Use the `api_token` stored in dlt secrets for authentication.

# Resources

## Media Categories
- **Endpoint Path**: `/v2/media/categories`
- **Primary Key**: `id`
- **Data Selector**: `data`

## Media Search
- **Endpoint Path**: `/v2/media/search`
- **Primary Key**: `id`
- **Data Selector**: `data`
- **Pagination Strategy**: Page number
  - **Page Parameter**: `page`
  - **Limit Parameter**: `per_page`
  - **Limit**: 20

## User Details
- **Endpoint Path**: `/v2/user`
- **Primary Key**: `id`
- **Data Selector**: `data`

# Error Handling

- **Retry Logic**: Implement retry on HTTP status codes 429, 500, 502, 503, 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

## Media Categories
- **Fields**:
  - `id`: string
  - `name`: string or null

## Media Search
- **Fields**:
  - `id`: string
  - `description`: string or null
  - `assets`: object or null
    - `url`: string or null

## User Details
- **Fields**:
  - `id`: string
  - `username`: string or null
  - `language`: string or null

This configuration provides a structured approach to accessing media content and user details from a third-party API, ensuring data integrity and efficient data retrieval through pagination and error handling strategies.