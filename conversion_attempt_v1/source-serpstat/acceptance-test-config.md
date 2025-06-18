# Overview

This document provides a vendor-neutral guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports full refresh sync types, allowing you to retrieve data from the API in a structured manner.

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
- **Secrets or Config Values**: Use `api_key` stored securely in `dlt.secrets`.

## Resources

- **Resource Name**: `resource_name`
- **Endpoint Path and Method**: `/v1/resource` using the GET method
- **Pagination Strategy**: Offset pagination with a limit of 100 records per request
- **Data Selector**: `data`

## Error Handling

- **Retry/Backoff Logic**: Implement retry logic with exponential backoff for handling transient errors.
- **Known HTTP Response Codes**: Handle HTTP status codes such as 429, 500, 502, 503, and 504 with retries.
- **Fallback/Handling Behavior**: Ensure robust error handling to manage API rate limits and server errors.

## Schema

- **Fields**: Use neutral field names such as `analytics_id`, `customer_id`, etc.
- **Types and Nullability**: Define field types and nullability based on the API's response structure.

This guide ensures a clean and generalized approach to integrating with a third-party analytics API using dltHub's REST API source format, abstracting away any proprietary or branded content.