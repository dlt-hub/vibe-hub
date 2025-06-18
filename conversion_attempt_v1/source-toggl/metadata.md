# Overview

This document provides a configuration guide for integrating with a third-party time-tracking API using dltHub's REST API source. The integration allows for seamless data extraction from the API, supporting various synchronization types and configurations.

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
            "name": "time_entries",
            "endpoint": {
                "path": "/v1/time_entries",
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
- **Secrets or Config Values**: Use `dlt.secrets["api_key"]` to securely store and access your API key.

# Resources

- **Resource Name**: `time_entries`
- **Endpoint Path and Method**: `/v1/time_entries`, using the default HTTP GET method.
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

# Error Handling

- **Retry/Backoff Logic**: Implement retry logic for handling transient errors, with exponential backoff.
- **Known HTTP Response Codes**: Handle common HTTP status codes such as 429 (Too Many Requests) and 500 (Internal Server Error) with retries.
- **Fallback/Handling Behavior**: Ensure robust error handling to manage API rate limits and server errors gracefully.

# Schema

- **Fields**: The schema includes fields such as `id`, `start_time`, `end_time`, and `duration`.
- **Types and Nullability**: Ensure that field types and nullability are consistent with the API's data model.
- **Neutral Field Names**: Use generic field names like `entry_id` instead of branded identifiers.

This configuration provides a clean, vendor-neutral setup for integrating with a third-party time-tracking API using dltHub's REST API source. It abstracts away any proprietary or branded content, ensuring a seamless and open-source-friendly integration.