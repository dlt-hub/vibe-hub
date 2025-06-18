# Overview

This document provides a vendor-neutral guide for integrating with a third-party analytics API using the dltHub REST API source format. The integration supports both full refresh and incremental sync types, allowing for flexible data retrieval from the API.

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
- **Endpoint Path and Method**: `/v1/resource`, default method is `GET`
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request
- **Data Selector**: `data`

## Error Handling

- **Retry/Backoff Logic**: Implement retry logic with exponential backoff for handling transient errors.
- **Known HTTP Response Codes**: Handle common HTTP status codes such as 429 (Too Many Requests), 500 (Internal Server Error), 502 (Bad Gateway), 503 (Service Unavailable), and 504 (Gateway Timeout).
- **Fallback/Handling Behavior**: Implement fallback mechanisms for handling API rate limits and server errors.

## Schema

- **Fields**: Use neutral field names such as `customer_id`, `event_timestamp`, and `event_type`.
- **Types**: Ensure data types are consistent with the API's response schema.
- **Nullability**: Define fields as nullable based on the API's data model.

This guide ensures a clean and reusable integration pattern for accessing third-party analytics APIs using dltHub's REST API source format, abstracting away any proprietary or branded content.