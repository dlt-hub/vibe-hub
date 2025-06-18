# Overview

This document provides a configuration guide for integrating with a third-party scheduling API using dltHub's REST API source. The integration allows for seamless data extraction from the API, supporting various synchronization types and pagination strategies.

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
            "name": "schedule_resource",
            "endpoint": {
                "path": "/v1/schedule",
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
- **Secrets or Config Values**: Use `api_key` stored securely in dlt's secrets management.

## Resources

- **Resource Name**: `schedule_resource`
- **Endpoint Path and Method**: `/v1/schedule`, default method is GET.
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: `data`

## Error Handling

- **Retry/Backoff Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504 with a maximum of 3 retries and a backoff factor of 2.
- **Known HTTP Response Codes**: Handle standard HTTP error codes with appropriate retry mechanisms.
- **Fallback/Handling Behavior**: Ensure robust error handling to manage API rate limits and server errors.

## Schema

- **Fields**: Use neutral field names such as `schedule_id`, `event_name`, `start_time`, etc.
- **Types and Nullability**: Define field types and nullability based on the API's response structure, ensuring consistency and accuracy in data representation.

This configuration provides a clean, vendor-neutral setup for integrating with a third-party scheduling API using dltHub's REST API source, ensuring efficient data extraction and management.