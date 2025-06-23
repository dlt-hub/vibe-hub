# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports data synchronization from various resources, including submissions, templates, and submitters. The API uses cursor-based pagination and requires API key authentication.

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
            "name": "submissions",
            "endpoint": {
                "path": "/submissions",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "cursor_path": "pagination.next"
                }
            }
        },
        {
            "name": "templates",
            "endpoint": {
                "path": "/templates",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "cursor_path": "pagination.next"
                }
            }
        },
        {
            "name": "submitters",
            "endpoint": {
                "path": "/submitters",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "after",
                    "cursor_path": "pagination.next"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: Use `api_key` stored in `dlt.secrets["api_key"]`.
- **Location**: The API key is included in the request header as `X-Auth-Token`.

## Resources

### Submissions

- **Endpoint Path**: `/submissions`
- **HTTP Method**: GET
- **Pagination Strategy**: Cursor-based
  - **Cursor Parameter**: `after`
  - **Cursor Path**: `pagination.next`
- **Data Selector**: `data`

### Templates

- **Endpoint Path**: `/templates`
- **HTTP Method**: GET
- **Pagination Strategy**: Cursor-based
  - **Cursor Parameter**: `after`
  - **Cursor Path**: `pagination.next`
- **Data Selector**: `data`

### Submitters

- **Endpoint Path**: `/submitters`
- **HTTP Method**: GET
- **Pagination Strategy**: Cursor-based
  - **Cursor Parameter**: `after`
  - **Cursor Path**: `pagination.next`
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

### Submissions

- **Fields**: Includes `id`, `updated_at`, `created_at`, `status`, among others.
- **Types**: Various types including `string`, `number`, and `array`.
- **Nullability**: Fields can be nullable.

### Templates

- **Fields**: Includes `id`, `updated_at`, `created_at`, `name`, among others.
- **Types**: Various types including `string`, `number`, and `array`.
- **Nullability**: Fields can be nullable.

### Submitters

- **Fields**: Includes `id`, `updated_at`, `created_at`, `email`, among others.
- **Types**: Various types including `string`, `number`, and `array`.
- **Nullability**: Fields can be nullable.

This configuration provides a comprehensive setup for integrating with the API, ensuring data is synchronized efficiently and securely.