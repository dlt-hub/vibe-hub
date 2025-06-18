# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source. The integration allows for data extraction from a specified API endpoint, supporting various authentication methods and pagination strategies.

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
- **Secrets or Config Values**: Use neutral keys such as `api_key`, `token`, or `client_id` for storing sensitive information.

## Resources

- **Resource Name**: `resource_name`
- **Endpoint Path and Method**: `/v1/resource` (default method: GET)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry/Backoff Logic**: Implement retry logic for handling transient errors with exponential backoff.
- **Known HTTP Response Codes**: Handle common HTTP status codes such as 429 (Too Many Requests), 500 (Internal Server Error), 502 (Bad Gateway), 503 (Service Unavailable), and 504 (Gateway Timeout).
- **Fallback/Handling Behavior**: Define fallback mechanisms for handling errors and ensuring data consistency.

## Schema

- **Fields, Types, Nullability**: Define the schema with neutral field names such as `analytics_id` instead of proprietary identifiers.
- **Use Neutral Field Names**: Ensure that field names are generalized and do not contain any brand-specific terms.

This configuration guide provides a clean, vendor-neutral approach to integrating with a third-party analytics API using dltHub's REST API source. By following the outlined structure and parameters, users can effectively extract and manage data from the specified API endpoint.