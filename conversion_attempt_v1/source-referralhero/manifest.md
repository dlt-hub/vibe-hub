# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports data synchronization from various resources, including lists, leaderboard, bonuses, and subscribers. The API uses pagination and requires API key authentication.

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
            "name": "lists",
            "endpoint": {
                "path": "/v1/resource",
                "data_selector": "data.lists",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "page_size",
                    "limit": 50,
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "leaderboard",
            "endpoint": {
                "path": "/v1/resource/{{stream_partition.uuid_id}}/leaderboard",
                "data_selector": "data.ranking",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page_size",
                    "limit_param": "page",
                    "limit": 50,
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "bonuses",
            "endpoint": {
                "path": "/v1/resource/{{stream_partition.uuid_id}}/bonuses",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "page_size",
                    "limit": 50,
                    "start_from_page": 0
                }
            }
        },
        {
            "name": "subscribers",
            "endpoint": {
                "path": "/v1/resource/{{stream_partition.uuid_id}}/subscribers",
                "data_selector": "data.subscribers",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "page_size",
                    "limit": 50,
                    "start_from_page": 1,
                    "inject_on_first_request": True
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: Use `api_key` stored in `dlt.secrets["api_key"]`
- **Location**: Query parameter

# Resources

## Lists
- **Endpoint Path**: `/v1/resource`
- **Data Selector**: `data.lists`
- **Pagination Strategy**: Page number with `page` and `page_size` parameters, starting from page 1

## Leaderboard
- **Endpoint Path**: `/v1/resource/{{stream_partition.uuid_id}}/leaderboard`
- **Data Selector**: `data.ranking`
- **Pagination Strategy**: Page number with `page_size` and `page` parameters, starting from page 1

## Bonuses
- **Endpoint Path**: `/v1/resource/{{stream_partition.uuid_id}}/bonuses`
- **Data Selector**: `data`
- **Pagination Strategy**: Page number with `page` and `page_size` parameters, starting from page 0

## Subscribers
- **Endpoint Path**: `/v1/resource/{{stream_partition.uuid_id}}/subscribers`
- **Data Selector**: `data.subscribers`
- **Pagination Strategy**: Page number with `page` and `page_size` parameters, starting from page 1, with injection on the first request

# Error Handling

- **Retry/Backoff Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

## Lists
- **Fields**:
  - `type`: string or null
  - `created_at`: number or null
  - `name`: string or null
  - `subscribers`: number or null
  - `uuid`: string (required)

## Leaderboard
- **Fields**: General object with additional properties allowed

## Bonuses
- **Fields**: General object with additional properties allowed

## Subscribers
- **Fields**: General object with additional properties allowed

This configuration provides a clean, vendor-neutral setup for integrating with a third-party analytics API using dltHub's REST API source format.