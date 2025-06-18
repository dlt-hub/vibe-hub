# Overview

This document provides a configuration guide for integrating with a third-party SMS API using dltHub's REST API source. The integration allows for seamless data extraction from the API, supporting various synchronization types as defined by the API's capabilities.

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
            "name": "sms_messages",
            "endpoint": {
                "path": "/v1/messages",
                "data_selector": "messages",
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

- **Resource Name**: `sms_messages`
- **Endpoint Path and Method**: `/v1/messages`, default method is GET.
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is selected from the `messages` field in the API response.

# Error Handling

- **Retry/Backoff Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504 with a maximum of 3 retries and a backoff factor of 2.
- **Known HTTP Response Codes**: Handle standard HTTP error codes with appropriate retry mechanisms.

# Schema

- **Fields**: The schema should include fields such as `id`, `message_content`, `timestamp`, etc., with types and nullability defined as per the API's response structure.
- **Neutral Field Names**: Use generic field names like `message_id` instead of any branded identifiers.

This configuration ensures a clean, vendor-neutral integration with the SMS API, following dltHub's REST API source format and terminology.