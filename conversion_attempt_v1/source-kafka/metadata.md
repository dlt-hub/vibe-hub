# Overview

This document provides a vendor-neutral configuration for integrating with a third-party message broker API using dltHub's REST API source. The integration supports data synchronization from the message broker to your data pipeline.

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
            "name": "message_broker_resource",
            "endpoint": {
                "path": "/v1/messages",
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

- **Resource Name**: `message_broker_resource`
- **Endpoint Path and Method**: `/v1/messages`, default method is GET.
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: `data`

# Error Handling

- **Retry/Backoff Logic**: Implement retry logic with exponential backoff for handling transient errors.
- **Known HTTP Response Codes**: Handle HTTP status codes such as 429 (Too Many Requests), 500 (Internal Server Error), 502 (Bad Gateway), 503 (Service Unavailable), and 504 (Gateway Timeout) with retries.
- **Fallback/Handling Behavior**: Ensure robust error handling to manage API rate limits and service disruptions.

# Schema

- **Fields**: Use neutral field names such as `message_id`, `timestamp`, `content`, etc.
- **Types and Nullability**: Define field types and nullability based on the API's response schema, ensuring compatibility with your data pipeline.

This configuration provides a clean, generalized setup for integrating with a message broker API using dltHub's REST API source, abstracting away any proprietary or branded content.