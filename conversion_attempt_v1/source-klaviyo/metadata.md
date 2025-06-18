# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source. The integration supports various data resources, allowing for efficient data synchronization and management.

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
            "name": "events",
            "endpoint": {
                "path": "/v1/events",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "campaigns",
            "endpoint": {
                "path": "/v1/campaigns",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "lists",
            "endpoint": {
                "path": "/v1/lists",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "metrics",
            "endpoint": {
                "path": "/v1/metrics",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "flows",
            "endpoint": {
                "path": "/v1/flows",
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
- **Configuration**: The API key is stored securely and accessed via `dlt.secrets["api_key"]`. It is included in the query parameters of each request.

## Resources

- **Resource Names**: events, campaigns, lists, metrics, flows
- **Endpoint Paths**: Each resource has a corresponding endpoint path, such as `/v1/events` for the events resource.
- **Pagination Strategy**: Uses an offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors. It retries on HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Implements exponential backoff with a factor of 2 and a maximum of 3 retries.

## Schema

- **Fields**: The schema for each resource is defined based on the API's response structure. Field names are generalized to ensure neutrality, such as using `analytics_id` instead of any branded identifiers.
- **Types and Nullability**: Each field's type and nullability are determined by the API's response and are configured accordingly.

This configuration guide provides a clean, vendor-neutral setup for integrating with a third-party analytics API using dltHub's REST API source. It ensures efficient data synchronization while maintaining flexibility and adaptability to different API structures.