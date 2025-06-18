# Overview

This document provides a configuration guide for integrating with a third-party test management API using dltHub's REST API source. The integration allows for seamless data extraction from the API, supporting various sync types and configurations.

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
            "name": "test_cases",
            "endpoint": {
                "path": "/v1/test_cases",
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

- **Resource Name**: `test_cases`
- **Endpoint Path and Method**: `/v1/test_cases`, default method is `GET`.
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry/Backoff Logic**: Implement retry logic for handling transient errors.
- **Known HTTP Response Codes**: Handle common HTTP status codes such as 429 (Too Many Requests) and 500 (Internal Server Error) with appropriate retry strategies.
- **Fallback/Handling Behavior**: Use exponential backoff for retries to manage rate limits and server errors effectively.

## Schema

- **Fields**: The schema includes fields such as `id`, `name`, `status`, etc.
- **Types and Nullability**: Ensure fields are typed appropriately, with nullability handled as per the API's response structure.
- **Neutral Field Names**: Use generic field names like `test_case_id` instead of any branded identifiers.

This configuration ensures a clean, vendor-neutral integration with the test management API, following dltHub's REST API source format and best practices.