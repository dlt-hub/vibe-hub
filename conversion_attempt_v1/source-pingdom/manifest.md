# Overview

This document provides a configuration guide for integrating with a third-party monitoring API using dltHub's REST API source format. The integration supports syncing data from two core resources: checks and performance metrics. The API requires an API key for authentication and supports incremental data fetching based on a specified start date.

## Configuration Example

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
            "name": "checks",
            "endpoint": {
                "path": "/v1/checks",
                "data_selector": "checks",
                "paginator": {
                    "type": "offset",
                    "limit": 25000,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "performance",
            "endpoint": {
                "path": "/v1/summary.performance/{check_id}",
                "params": {
                    "probes": dlt.config.get("probes"),
                    "resolution": dlt.config.get("resolution", "hour"),
                    "includeuptime": "true"
                },
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "from",
                    "cursor_path": "stream_end_time"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Bearer Token
- **Token**: Retrieved from dlt secrets using the key `api_key`.

## Resources

### Checks

- **Endpoint Path**: `/v1/checks`
- **HTTP Method**: GET
- **Pagination Strategy**: Offset
  - **Limit**: 25000
  - **Offset Parameter**: `offset`
- **Data Selector**: `checks`

### Performance

- **Endpoint Path**: `/v1/summary.performance/{check_id}`
- **HTTP Method**: GET
- **Parameters**:
  - `probes`: Optional, filter results by probe identifiers.
  - `resolution`: Optional, interval size (`hour`, `day`, `week`).
  - `includeuptime`: Always set to `true`.
- **Pagination Strategy**: Cursor
  - **Cursor Parameter**: `from`
  - **Cursor Path**: `stream_end_time`
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: Implemented with exponential backoff.
- **Retry on Status Codes**: 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

- **Checks Resource**: 
  - Primary Key: `id`
  - Additional properties are allowed.
- **Performance Resource**: 
  - Primary Key: `check_id`
  - Additional properties are allowed.

This configuration ensures a robust and flexible integration with the monitoring API, allowing for efficient data synchronization and error handling.