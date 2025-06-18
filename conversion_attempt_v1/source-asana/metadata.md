# Overview

This document provides a configuration guide for integrating with a third-party task management API using dltHub's REST API source. The integration allows for data extraction from the API, supporting various resource configurations and authentication methods.

# Configuration Example

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

# Authentication

- **Type**: `api_key`
- **Configuration**: The API key is stored securely using `dlt.secrets["api_key"]` and is included in the query parameters of each request.

# Resources

- **Resource Name**: `task_data`
- **Endpoint Path**: `/v1/tasks`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 items per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

# Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors. It retries on HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 is applied, with a maximum of 3 retries.

# Schema

- **Fields**: The schema includes fields such as `id`, `name`, and other task-related attributes.
- **Field Types**: All field types are generalized and neutral, ensuring compatibility with various data processing systems.
- **Nullability**: Fields are nullable unless specified otherwise in the API documentation.

This configuration provides a clean and vendor-neutral setup for integrating with a task management API, ensuring compatibility with dltHub's REST API source framework.