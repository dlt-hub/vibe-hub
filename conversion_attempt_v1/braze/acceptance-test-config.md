# Overview

This document provides a vendor-neutral configuration for integrating with a third-party analytics API using dltHub's REST API source. The integration supports both full refresh and incremental data synchronization, allowing for efficient data extraction and management.

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
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "daily_new_users",
            "endpoint": {
                "path": "/v1/daily_new_users",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "events_analytics",
            "endpoint": {
                "path": "/v1/events_analytics",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "daily_app_uninstalls",
            "endpoint": {
                "path": "/v1/daily_app_uninstalls",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "daily_active_users",
            "endpoint": {
                "path": "/v1/daily_active_users",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets**: The API key is stored securely and accessed via `dlt.secrets["api_key"]`.
- **Location**: The API key is included in the query parameters of each request.

## Resources

- **Resource Names**: `daily_new_users`, `events_analytics`, `daily_app_uninstalls`, `daily_active_users`
- **Endpoint Paths**: Each resource has a unique endpoint path under `/v1/`.
- **Pagination Strategy**: Uses an offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry Logic**: Implement retry logic for handling transient errors with exponential backoff.
- **HTTP Status Codes**: Retry on status codes such as 429 (Too Many Requests), 500 (Internal Server Error), 502 (Bad Gateway), 503 (Service Unavailable), and 504 (Gateway Timeout).
- **Max Retries**: Set a maximum of 3 retries with a backoff factor of 2.

## Schema

- **Fields**: The schema includes fields such as `id`, `timestamp`, `user_key`, and other relevant data points.
- **Types**: Ensure all fields are typed appropriately (e.g., integers, strings, timestamps).
- **Nullability**: Define which fields can be null based on the API's response structure.

This configuration provides a robust framework for integrating with a third-party analytics API, ensuring data is synchronized efficiently and accurately.