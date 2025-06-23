# Overview

This document provides a guide for integrating with a third-party analytics API using a REST API source configuration. The integration supports both full refresh and incremental data syncs, allowing for efficient data retrieval and updates.

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

The API requires an API key for authentication. The key should be included in the query parameters of each request.

- **Type**: `api_key`
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access your API key.

## Resources

- **Resource Name**: A generic name for the data resource.
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is located under the "data" field in the response.

## Error Handling

The integration includes retry logic for handling transient errors. The following HTTP status codes will trigger a retry:

- **Retry on Status Codes**: 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Factor**: 2 (exponential backoff)

## Schema

The schema for the data includes fields with neutral names to ensure compatibility and generalization. Each field's type and nullability should be defined based on the API's response structure. Use generic field names like `customer_id` or `event_timestamp` to maintain neutrality.