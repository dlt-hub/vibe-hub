# Overview

This document provides a vendor-neutral configuration for integrating with a third-party event management API. The integration supports data synchronization through a REST API, allowing for efficient data retrieval and management.

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
            "name": "events",
            "endpoint": {
                "path": "/v1/events",
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

- **Type**: `api_key`
- **Secrets or Config Values**: Use `api_key` stored securely in `dlt.secrets`.

## Resources

- **Resource Name**: `events`
- **Endpoint Path and Method**: `/v1/events`, default method is `GET`
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request
- **Data Selector**: `data`

## Error Handling

- **Retry/Backoff Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504.
- **Known HTTP Response Codes**: Handle standard HTTP error codes with appropriate retry mechanisms.
- **Fallback/Handling Behavior**: Use exponential backoff with a maximum of 3 retries.

## Schema

- **Fields**: Use neutral field names such as `event_id`, `event_name`, `event_date`.
- **Types and Nullability**: Define fields with appropriate data types and nullability based on the API's response structure.

This configuration provides a clean and generalized setup for integrating with a third-party event management API using dltHub's REST API source format. It abstracts away any proprietary or branded content, ensuring a vendor-neutral approach.