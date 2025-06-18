# Overview

This document provides a configuration guide for integrating with a third-party inventory management API using dltHub's REST API source. The integration supports data synchronization through RESTful endpoints, allowing for efficient data extraction and management.

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
            "name": "inventory_items",
            "endpoint": {
                "path": "/v1/inventory",
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

- **Type**: API Key
- **Configuration**: The API key is stored securely and accessed via `dlt.secrets["api_key"]`. It is included in the query parameters of each request.

## Resources

- **Resource Name**: inventory_items
- **Endpoint Path**: `/v1/inventory`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 items per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry Logic**: Implemented with exponential backoff for handling transient errors.
- **HTTP Status Codes**: Configured to retry on status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3 attempts with a backoff factor of 2.

## Schema

- **Fields**: The schema includes fields such as `id`, `name`, `quantity`, and other inventory-related attributes.
- **Types**: Fields are typed according to their data nature (e.g., integer, string).
- **Nullability**: Fields are marked as nullable where applicable to accommodate incomplete data.

This configuration provides a robust framework for interacting with a third-party inventory management API, ensuring data is synchronized efficiently and reliably.