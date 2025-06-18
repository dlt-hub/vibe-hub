```markdown
# Overview

This document provides a configuration guide for integrating with a third-party message queue API using dltHub's REST API source. The integration supports data synchronization through a RESTful interface, allowing for efficient data retrieval and management.

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
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access your API key.
- **Location**: The API key is included in the query parameters of each request.

## Resources

- **Resource Name**: `message_queue`
- **Endpoint Path**: `/v1/messages`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `messages` field in the API response.

## Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors.
- **HTTP Status Codes**: Retries are triggered for status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 is applied, with a maximum of 3 retries.

## Schema

- **Fields**: The schema includes fields such as `id`, `message_content`, and `timestamp`.
- **Types**: Ensure that field types are consistent with the API's response format.
- **Nullability**: Fields are nullable unless specified otherwise in the API documentation.

This configuration provides a clean and efficient way to integrate with a third-party message queue API, leveraging dltHub's REST API source capabilities for seamless data synchronization.
```