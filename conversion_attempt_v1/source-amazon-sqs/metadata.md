# Overview

This document provides a configuration guide for integrating with a third-party message queue API using dltHub's REST API source format. The integration allows for seamless data extraction from the message queue service, supporting various sync types and configurations.

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
            "name": "message_queue",
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

## Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: Use `api_key` stored securely in dlt's secrets management.

## Resources

- **Resource Name**: `message_queue`
- **Endpoint Path and Method**: `/v1/messages`, default method is GET.
- **Pagination Strategy**: Offset-based pagination with a limit of 100 items per request.
- **Data Selector**: The data is selected from the `messages` field in the API response.

## Error Handling

- **Retry/Backoff Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504 with a maximum of 3 retries and a backoff factor of 2.
- **Known HTTP Response Codes**: Handle standard HTTP response codes with appropriate retry and error handling mechanisms.

## Schema

- **Fields**: Use neutral field names such as `message_id`, `timestamp`, and `content`.
- **Types and Nullability**: Define field types and nullability based on the API response schema, ensuring compatibility with dltHub's data processing.

This configuration provides a clean and vendor-neutral setup for integrating with a third-party message queue API using dltHub's REST API source format. Ensure all parameters are sourced from the provided YAML and generalized to remove any proprietary or branded content.