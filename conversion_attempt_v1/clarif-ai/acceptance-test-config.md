# Overview

This document provides a vendor-neutral configuration for integrating with a third-party analytics API using the dltHub REST API source. The integration supports data synchronization through RESTful endpoints, allowing for efficient data extraction and management.

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

- **Retry Logic**: The configuration includes retry logic for handling transient errors. The system will retry requests on specific HTTP status codes (e.g., 429, 500, 502, 503, 504) with a maximum of 3 retries and an exponential backoff factor of 2.

## Schema

- **Fields**: The schema includes fields with neutral names such as `id`, `customer_id`, and `timestamp`.
- **Types and Nullability**: Each field's type and nullability are defined based on the API's response structure, ensuring compatibility and data integrity.

This configuration provides a clean and efficient way to interact with a third-party analytics API, abstracting away any proprietary or branded content to ensure a seamless integration experience.