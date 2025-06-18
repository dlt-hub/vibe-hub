# Overview

This document provides a vendor-neutral configuration for integrating with a third-party learning platform's REST API. The integration supports data synchronization using a RESTful approach, allowing for efficient data retrieval and management.

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
            "name": "resource_name",
            "endpoint": {
                "path": "/v1/resource",
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
- **Configuration**: The API key is stored securely and accessed via `dlt.secrets["api_key"]`. The key is included in the query parameters of each request.

## Resources

- **Resource Name**: `resource_name`
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: `GET` (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry Logic**: Implemented with exponential backoff for handling transient errors.
- **HTTP Response Codes**: The integration is designed to handle common HTTP status codes such as 429 (Too Many Requests) and 5xx server errors with retries.
- **Fallback Behavior**: In case of persistent errors, the integration will log the issue for further investigation.

## Schema

- **Fields**: The schema includes fields such as `id`, `name`, and `description`, with types and nullability defined based on the API's response structure.
- **Field Names**: Use neutral and descriptive names like `resource_id` and `resource_name` to ensure clarity and consistency.

This configuration provides a clean and efficient way to interact with a third-party learning platform's API, ensuring data is synchronized accurately and reliably.