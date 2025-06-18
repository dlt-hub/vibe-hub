# Overview

This document provides a guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports data synchronization through a RESTful API, allowing for efficient data extraction and management.

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

The integration uses API key authentication. The API key should be stored securely and accessed via `dlt.secrets`.

- **Type**: `api_key`
- **Key Name**: `api_key`
- **Location**: `query`

## Resources

- **Resource Name**: `resource_name`
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

The integration includes basic error handling with retry logic for common HTTP status codes that indicate temporary issues.

- **Retry on Status Codes**: 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

The schema for the data extracted from the API is defined with neutral field names. Each field's type and nullability are consistent with the API's response structure.

- **Primary Key**: `id`
- **Field Names**: Use generic names such as `customer_id`, `event_type`, etc.

This guide ensures a clean and vendor-neutral integration with a third-party analytics API using dltHub's REST API source format, abstracting away any proprietary or branded content.