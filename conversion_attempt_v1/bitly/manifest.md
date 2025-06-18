# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration allows for automated data retrieval and synchronization from various resources, supporting both full and incremental sync types.

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
            "name": "resource_name",
            "endpoint": {
                "path": "/v1/resource",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 50,
                    "cursor_param": "search_after"
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

## Resource: Bitlinks

- **Endpoint Path**: `/v4/groups//{{ stream_partition['group_guid'] }}/bitlinks`
- **HTTP Method**: GET
- **Data Selector**: `links`
- **Primary Key**: `id`
- **Pagination Strategy**: Cursor
  - **Cursor Parameter**: `search_after`
  - **Page Size**: 50

## Resource: Bitlink Clicks

- **Endpoint Path**: `/v4/bitlinks/{{ stream_partition['bitlink'] }}/clicks`
- **HTTP Method**: GET
- **Data Selector**: None (empty path)

## Resource: Campaigns

- **Endpoint Path**: `/v4/campaigns`
- **HTTP Method**: GET
- **Data Selector**: `campaigns`
- **Primary Key**: `guid`
- **Incremental Sync**: Based on `modified` field

## Resource: Channels

- **Endpoint Path**: `/v4/channels`
- **HTTP Method**: GET
- **Data Selector**: `channels`
- **Primary Key**: `guid`
- **Incremental Sync**: Based on `modified` field

## Resource: Groups

- **Endpoint Path**: `/v4/groups`
- **HTTP Method**: GET
- **Data Selector**: `groups`
- **Primary Key**: `guid`
- **Incremental Sync**: Based on `modified` field

# Error Handling

- **Retry Logic**: Implement retry on specific HTTP status codes such as 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

The schema for each resource is defined with neutral field names and types. For example:

## Bitlinks Schema

- **archived**: boolean or null
- **created_at**: string
- **id**: string (required)
- **link**: string or null
- **long_url**: string or null

## Bitlink Clicks Schema

- **link_clicks**: array or null
  - **clicks**: number or null
  - **date**: string or null

## Campaigns Schema

- **campaigns**: array or null

## Channels Schema

- **channels**: array or null

## Groups Schema

- **guid**: string (required)
- **modified**: string (required)

This configuration provides a clean, vendor-neutral setup for integrating with a third-party analytics API using dltHub's REST API source. All parameters and patterns are directly sourced from the provided YAML configuration, ensuring no proprietary or branded content is included.