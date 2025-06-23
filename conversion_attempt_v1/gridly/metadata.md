# Overview

This document provides a configuration guide for integrating with a third-party API using dltHub's REST API source. The integration supports data synchronization from the API, allowing for efficient data extraction and management.

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
- **Secrets or Config Values**: Use `dlt.secrets["api_key"]` to securely manage your API key.
- **Location**: The API key is included in the query parameters of the request.

## Resources

- **Resource Name**: `resource_name`
- **Endpoint Path and Method**: The API endpoint is accessed via the path `/v1/resource` using the GET method.
- **Pagination Strategy**: The integration uses an offset-based pagination strategy with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry/Backoff Logic**: The integration includes retry logic for handling transient errors, with a maximum of 3 retries and an exponential backoff factor of 2.
- **Known HTTP Response Codes**: The integration is configured to retry on HTTP status codes 429, 500, 502, 503, and 504.
- **Fallback/Handling Behavior**: In case of repeated failures, the integration will log the error and halt further requests to prevent excessive load on the API.

## Schema

- **Fields**: The schema includes fields such as `id`, `name`, and `value`, with types and nullability defined based on the API's response structure.
- **Neutral Field Names**: Use generic field names like `customer_id` or `user_key` to maintain vendor neutrality.

This configuration guide ensures a clean and efficient integration with a third-party API using dltHub's REST API source, abstracting away any vendor-specific details and focusing on reusable patterns.