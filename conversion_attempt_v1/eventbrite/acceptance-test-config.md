# Overview

This document provides a vendor-neutral configuration for integrating with a third-party event management API using dltHub's REST API source. The integration supports data synchronization through RESTful endpoints, allowing for efficient data extraction and management.

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
            "name": "event_data",
            "endpoint": {
                "path": "/v1/events",
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
- **Configuration**: The API key is stored securely using `dlt.secrets["api_key"]` and is included in the query parameters of each request.

## Resources

- **Resource Name**: `event_data`
- **Endpoint Path**: `/v1/events`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry Logic**: Implemented with exponential backoff for handling transient errors.
- **HTTP Status Codes**: Configured to retry on status codes such as 429 (Too Many Requests), 500 (Internal Server Error), 502 (Bad Gateway), 503 (Service Unavailable), and 504 (Gateway Timeout).
- **Max Retries**: 3 attempts with a backoff factor of 2.

## Schema

- **Fields**: The schema includes fields such as `id`, `name`, `start_time`, and `end_time`, with types and nullability defined based on the API's response structure.
- **Field Names**: Use neutral and descriptive names like `event_id`, `event_name`, `start_time`, and `end_time` to ensure clarity and consistency.

This configuration provides a clean and efficient way to interact with a third-party event management API, abstracting away any vendor-specific details and focusing on the essential integration patterns.