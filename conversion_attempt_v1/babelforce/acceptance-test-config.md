# Overview

This document provides a vendor-neutral guide for integrating with a third-party analytics API using the dltHub REST API source. The integration supports both full refresh and incremental sync types, allowing for flexible data retrieval from the API.

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
- **Secrets or Config Values**: Use `dlt.secrets["api_key"]` to securely store and access your API key.

## Resources

- **Resource Name**: `resource_name`
- **Endpoint Path and Method**: `/v1/resource`, default method is GET.
- **Pagination Strategy**: Offset pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry/Backoff Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504 with a maximum of 3 retries and a backoff factor of 2.
- **Known HTTP Response Codes**: Handle common HTTP errors gracefully to ensure robust data retrieval.

## Schema

- **Fields**: Use neutral field names such as `customer_id` or `user_key` to represent unique identifiers.
- **Types and Nullability**: Ensure that the schema is consistent with the API's data structure, using appropriate data types and handling null values as necessary.

This guide ensures a clean and generalized approach to integrating with a third-party analytics API, free from any proprietary or branded content.