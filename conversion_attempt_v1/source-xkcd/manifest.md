# Overview

This document provides a configuration example for integrating with a third-party API that provides comic data. The integration is designed to retrieve comic information using a REST API, with support for pagination and error handling. The API does not require authentication.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "none"
        }
    },
    "resource_defaults": {
        "primary_key": "comic_id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "comics",
            "endpoint": {
                "path": "/{comic_number}/info.0.json",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "comic_number",
                    "cursor_value": "{response['comic_id']+1}",
                    "stop_condition": "not response.get('comic_id')"
                },
                "error_handler": {
                    "retry_on_status_codes": [500, 502, 503, 504],
                    "max_retries": 5,
                    "backoff_factor": 2,
                    "ignore_status_codes": [404]
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: None
- This API does not require authentication for access.

## Resources

- **Resource Name**: comics
- **Endpoint Path**: `/{comic_number}/info.0.json`
- **HTTP Method**: GET
- **Pagination Strategy**: Cursor-based pagination using the comic number as the cursor.
- **Data Selector**: The data is extracted directly from the response.

## Error Handling

- **Retry Logic**: The integration will retry up to 5 times for server errors (HTTP status codes 500, 502, 503, 504) with an exponential backoff strategy.
- **Ignore Logic**: HTTP 404 errors are ignored, allowing the process to continue without interruption.

## Schema

The schema for the comic data includes the following fields:

- **alt**: Nullable string
- **day**: Nullable string
- **img**: Nullable string
- **link**: Nullable string
- **month**: Nullable string
- **news**: Nullable string
- **comic_id**: Nullable integer (primary key)
- **safe_title**: Nullable string
- **title**: Nullable string
- **transcript**: Nullable string
- **year**: Nullable string

This configuration provides a robust setup for extracting comic data from the API, handling pagination, and managing errors effectively.