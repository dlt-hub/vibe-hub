# Overview

This document provides a vendor-neutral configuration for integrating with a third-party scheduling API using dltHub's REST API source. The integration supports data synchronization from the API, allowing for efficient data retrieval and management.

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
            "name": "scheduling_data",
            "endpoint": {
                "path": "/v1/schedules",
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
- **Secrets or Config Values**: Use `api_key` stored securely in `dlt.secrets`.

# Resources

- **Resource Name**: `scheduling_data`
- **Endpoint Path and Method**: `/v1/schedules`, default method is GET.
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

# Error Handling

- **Retry/Backoff Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504 with a maximum of 3 retries and an exponential backoff factor of 2.
- **Known HTTP Response Codes**: Handle standard HTTP error codes with appropriate retry mechanisms.

# Schema

- **Fields**: Use neutral field names such as `schedule_id`, `employee_id`, `shift_start`, `shift_end`.
- **Types and Nullability**: Define field types and nullability based on the API's response structure, ensuring consistency and accuracy in data representation.

This configuration provides a clean and generalized setup for integrating with a scheduling API, ensuring compatibility with dltHub's REST API source framework.