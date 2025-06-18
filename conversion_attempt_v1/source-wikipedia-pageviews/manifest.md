# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration allows for the retrieval of pageview metrics on a per-article basis and top articles for a specified project. The API supports incremental synchronization based on timestamps.

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
        "primary_key": "timestamp",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "per_article",
            "endpoint": {
                "path": "/v1/resource/per-article/{project}/{access}/{agent}/{article}/daily/{start_time}/{end_time}",
                "data_selector": "items",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "timestamp",
                    "cursor_param": "timestamp"
                }
            }
        },
        {
            "name": "top_articles",
            "endpoint": {
                "path": "/v1/resource/top/{project}/{access}/{year}/{month}/{day}",
                "data_selector": "items"
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: Use `api_key` stored in `dlt.secrets`.

## Resources

### Per-Article

- **Resource Name**: `per_article`
- **Endpoint Path**: `/v1/resource/per-article/{project}/{access}/{agent}/{article}/daily/{start_time}/{end_time}`
- **HTTP Method**: GET
- **Pagination Strategy**: Cursor-based using `timestamp`
- **Data Selector**: `items`

### Top Articles

- **Resource Name**: `top_articles`
- **Endpoint Path**: `/v1/resource/top/{project}/{access}/{year}/{month}/{day}`
- **HTTP Method**: GET
- **Data Selector**: `items`

## Error Handling

- **Retry/Backoff Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504 with a maximum of 3 retries and a backoff factor of 2.

## Schema

### Per-Article Schema

- **Fields**:
  - `access`: string
  - `agent`: string
  - `article`: string
  - `granularity`: string
  - `project`: string
  - `timestamp`: string
  - `views`: integer (int64)

### Top Articles Schema

- **Fields**:
  - `access`: string
  - `articles`: array of objects
    - `article`: string
    - `rank`: integer (int32)
    - `views`: integer (int64)
  - `day`: string
  - `month`: string
  - `project`: string
  - `year`: string
  - `timestamp`: string

This configuration provides a clean and vendor-neutral setup for accessing pageview metrics from a third-party analytics API using dltHub's REST API source format.