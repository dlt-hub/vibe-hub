# Overview

This document provides a vendor-neutral configuration for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports data synchronization from the API, allowing for efficient data extraction and management.

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
- **Secrets or Config Values**: Use neutral keys such as `api_key` for storing sensitive information securely.

## Resources

- **Resource Name**: A generic name representing the data resource.
- **Endpoint Path and Method**: `/v1/resource` using the default HTTP method GET.
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data field is specified as `data` for extracting the relevant information from the API response.

## Error Handling

- **Retry/Backoff Logic**: Implement retry logic for handling transient errors with exponential backoff.
- **Known HTTP Response Codes**: Handle common HTTP status codes such as 429, 500, 502, 503, and 504.
- **Fallback/Handling Behavior**: Define fallback mechanisms for managing API rate limits and server errors.

## Schema

- **Fields, Types, Nullability**: Use neutral field names such as `analytics_id` to represent unique identifiers.
- **Consistency**: Ensure the schema is consistent with the API's data structure and uses generalized field names.

This configuration provides a clean and reusable pattern for integrating with a third-party analytics API, abstracting away any proprietary or branded content.