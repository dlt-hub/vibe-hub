# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration allows for the export of conversation data, supporting incremental synchronization based on a datetime cursor.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v1/",
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
            "name": "conversation_export",
            "endpoint": {
                "path": "/conversation_export",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [429, 500, 502, 503, 504],
                    "max_retries": 3,
                    "backoff_factor": 60
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Bearer Token
- **Token**: Use the `api_token` stored in dlt's secrets for authentication.

## Resources

- **Resource Name**: conversation_export
- **Endpoint Path**: `/conversation_export`
- **HTTP Method**: GET
- **Pagination Strategy**: Single page (no pagination)
- **Data Selector**: The data is extracted from the root of the response.

## Error Handling

- **Retry Logic**: The integration retries on HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: A constant backoff of 60 seconds is applied between retries.
- **Max Retries**: 3 attempts are made before failing.

## Schema

The schema defines the structure of the conversation data, with fields such as:

- **id**: Integer, nullable
- **created_at**: Integer, nullable
- **initial_channel**: String, nullable
- **requester_id**: String, nullable
- **updated_at**: Integer, nullable

Additional fields include arrays and nested objects for complex data types, all of which are nullable to accommodate missing data.

## Incremental Sync

- **Cursor Field**: `updated_at`
- **Cursor Format**: Milliseconds since epoch
- **Batch Size**: Configurable number of days per request, with a default of 31 days.
- **Start Date**: Records are pulled from the specified start date onwards, formatted as `YYYY-MM-DD`.

This configuration ensures a robust and flexible integration with the third-party analytics API, allowing for efficient data synchronization and error handling.