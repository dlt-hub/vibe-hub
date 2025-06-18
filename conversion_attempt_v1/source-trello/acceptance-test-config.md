# Overview

This document provides a vendor-neutral guide for integrating with a third-party task management API using dltHub's REST API source format. The integration supports various synchronization types, including incremental and full refresh, to efficiently manage and retrieve data from the API.

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
            "name": "task_data",
            "endpoint": {
                "path": "/v1/tasks",
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

- **Type**: API Key
- **Configuration**: The API key is stored securely using `dlt.secrets["api_key"]` and is included in the query parameters of each request.

## Resources

- **Resource Name**: task_data
- **Endpoint Path**: `/v1/tasks`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the "data" field in the API response.

## Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors. It retries on HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff is applied with a factor of 2, and a maximum of 3 retries is allowed.

## Schema

- **Fields**: The schema includes fields such as `id`, `name`, `description`, and `status`. These fields are generalized to ensure compatibility with various task management APIs.
- **Types and Nullability**: Each field's type and nullability are defined based on the API's response structure, ensuring accurate data representation.

This guide provides a clean and generalized approach to integrating with a task management API using dltHub's REST API source format, ensuring compatibility and ease of use across different environments.