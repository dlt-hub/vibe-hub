# Overview

This document provides a configuration guide for integrating with a third-party analytics API using the dltHub REST API source. The integration supports data synchronization from various resources, each with its own endpoint and schema. The API supports incremental synchronization based on datetime cursors.

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
            "name": "models",
            "endpoint": {
                "path": "/v1/models",
                "data_selector": "data"
            },
            "incremental_sync": {
                "type": "datetime_cursor",
                "cursor_field": "created",
                "start_datetime": dlt.config["start_date"],
                "datetime_format": "%Y-%m-%dT%H:%M:%SZ"
            }
        },
        {
            "name": "files",
            "endpoint": {
                "path": "/v1/files",
                "data_selector": "data"
            },
            "incremental_sync": {
                "type": "datetime_cursor",
                "cursor_field": "created_at",
                "start_datetime": dlt.config["start_date"],
                "datetime_format": "%Y-%m-%dT%H:%M:%SZ"
            }
        },
        {
            "name": "file_contents",
            "endpoint": {
                "path": "/v1/files/{{ stream_partition['file_id'] }}/content",
                "data_selector": []
            }
        },
        {
            "name": "batches",
            "endpoint": {
                "path": "/v1/batches",
                "data_selector": "data",
                "params": {
                    "limit": dlt.config["limit"]
                }
            },
            "incremental_sync": {
                "type": "datetime_cursor",
                "cursor_field": "created_at",
                "start_datetime": dlt.config["start_date"],
                "datetime_format": "%Y-%m-%dT%H:%M:%SZ"
            }
        },
        {
            "name": "batch_results",
            "endpoint": {
                "path": "/v1/batches",
                "data_selector": "data"
            },
            "incremental_sync": {
                "type": "datetime_cursor",
                "cursor_field": "created_at",
                "start_datetime": dlt.config["start_date"],
                "datetime_format": "%Y-%m-%dT%H:%M:%SZ"
            }
        }
    ]
})
```

## Authentication

- **Type**: Bearer
- **Token**: Use the `api_key` stored in `dlt.secrets`.

## Resources

### Models
- **Endpoint Path**: `/v1/models`
- **Data Selector**: `data`
- **Incremental Sync**: Based on `created` field with datetime format `%Y-%m-%dT%H:%M:%SZ`.

### Files
- **Endpoint Path**: `/v1/files`
- **Data Selector**: `data`
- **Incremental Sync**: Based on `created_at` field with datetime format `%Y-%m-%dT%H:%M:%SZ`.

### File Contents
- **Endpoint Path**: `/v1/files/{{ stream_partition['file_id'] }}/content`
- **Data Selector**: `[]`

### Batches
- **Endpoint Path**: `/v1/batches`
- **Data Selector**: `data`
- **Parameters**: `limit` from configuration
- **Incremental Sync**: Based on `created_at` field with datetime format `%Y-%m-%dT%H:%M:%SZ`.

### Batch Results
- **Endpoint Path**: `/v1/batches`
- **Data Selector**: `data`
- **Incremental Sync**: Based on `created_at` field with datetime format `%Y-%m-%dT%H:%M:%SZ`.

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

### Models
- **Fields**: `id` (string), `created` (number), `object` (string or null), `owned_by` (string or null)
- **Primary Key**: `id`

### Files
- **Fields**: `id` (string), `created_at` (number), `filename` (string or null), `bytes` (number or null), `purpose` (string or null)
- **Primary Key**: `id`

### File Contents
- **Fields**: `uuid` (string), `glossary` (object or null)
- **Primary Key**: `uuid`

### Batches
- **Fields**: `id` (string), `created_at` (number), `completion_window` (string or null), `status` (string or null)
- **Primary Key**: `id`

### Batch Results
- **Fields**: `id` (string), `created_at` (number), `completion_window` (string or null), `status` (string or null)
- **Primary Key**: `id`

This configuration provides a comprehensive setup for synchronizing data from a third-party analytics API using dltHub's REST API source.