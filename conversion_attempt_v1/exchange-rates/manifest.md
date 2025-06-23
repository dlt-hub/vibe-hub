# Overview

This document provides a configuration example for integrating with a third-party currency exchange rates API using the dltHub REST API source format. The integration allows for the retrieval of exchange rate data, supporting incremental synchronization based on date fields.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "api_key",
            "name": "apikey",
            "api_key": dlt.secrets["access_key"],
            "location": "header"
        }
    },
    "resource_defaults": {
        "primary_key": "date",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "exchange_rates",
            "endpoint": {
                "path": "/v1/resource",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            },
            "incremental_sync": {
                "type": "datetime_cursor",
                "cursor_field": "date",
                "start_datetime": dlt.config["start_date"],
                "datetime_format": "%Y-%m-%d"
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: 
  - `api_key`: Stored in `dlt.secrets["access_key"]`
  - Injected into the request header as `apikey`

## Resources

- **Resource Name**: `exchange_rates`
- **Endpoint Path and Method**: `/v1/resource`, HTTP method: GET
- **Pagination Strategy**: `single_page` (no pagination)
- **Data Selector**: `data`

## Error Handling

- **Retry/Backoff Logic**: Not specified in the YAML, default dltHub behavior applies.
- **Known HTTP Response Codes**: Not specified in the YAML, default dltHub behavior applies.
- **Fallback/Handling Behavior**: Not specified in the YAML, default dltHub behavior applies.

## Schema

The schema for the `exchange_rates` resource includes the following fields:

- **base**: Nullable string
- **date**: Nullable string
- **rates**: Nullable object with properties for various currency codes, each being a nullable number
- **success**: Nullable boolean
- **timestamp**: Nullable integer

The schema supports additional properties, allowing for flexibility in the data structure. The primary key for the resource is the `date` field, and the data is merged based on this key during synchronization.