# Overview

This document provides a configuration guide for integrating with a third-party email API using dltHub's REST API source format. The integration allows for seamless data extraction from the email service, supporting various sync types and configurations.

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
            "name": "email_messages",
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
- **Secrets or Config Values**: Use `api_key` stored securely in `dlt.secrets`.

## Resources

- **Resource Name**: `email_messages`
- **Endpoint Path and Method**: `/v1/messages`, default method is GET.
- **Pagination Strategy**: Offset-based pagination with a limit of 100 items per request.
- **Data Selector**: `messages`

## Error Handling

- **Retry/Backoff Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504.
- **Known HTTP Response Codes**: Handle standard HTTP error codes with appropriate retry mechanisms.
- **Fallback/Handling Behavior**: Use exponential backoff with a maximum of 3 retries.

## Schema

- **Fields**: Use neutral field names such as `email_id`, `subject`, `sender`, etc.
- **Types and Nullability**: Define fields with appropriate data types and specify if they are nullable.

This configuration ensures a clean and efficient integration with the email API, abstracting away any vendor-specific details and focusing on a generic, reusable setup.