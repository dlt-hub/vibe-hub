# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports data synchronization from the API's activity endpoint, utilizing pagination and incremental sync based on timestamps.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v1",
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
            "name": "activity",
            "endpoint": {
                "path": "/activity/{domain_id}",
                "params": {
                    "limit": 100,
                    "date_from": "{start_date}"
                },
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "page_size",
                    "limit": 100
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Bearer Token
- **Token**: Stored securely in `dlt.secrets` under the key `api_token`.

## Resources

- **Resource Name**: activity
- **Endpoint Path**: `/activity/{domain_id}`
- **HTTP Method**: GET
- **Pagination Strategy**: Page number-based pagination with parameters `page` and `page_size`.
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: Implemented with exponential backoff for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

The schema for the activity resource includes the following fields:

- **type**: string
- **created_at**: string (date-time format)
- **email**: object
  - **created_at**: string (date-time format)
  - **from**: string
  - **html**: nullable string
  - **id**: string
  - **recipient**: object
    - **created_at**: string (date-time format)
    - **deleted_at**: string
    - **email**: string
    - **id**: string
    - **updated_at**: string (date-time format)
  - **status**: string
  - **subject**: string
  - **tags**: nullable string
  - **text**: nullable string
  - **updated_at**: string (date-time format)
- **id**: string
- **updated_at**: string (date-time format)

This configuration ensures a clean and efficient integration with the third-party analytics API, leveraging dltHub's capabilities for data extraction and transformation.