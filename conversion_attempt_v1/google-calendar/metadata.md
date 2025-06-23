# Overview

This document provides a configuration guide for integrating with a third-party calendar API using a REST API source. The integration allows for the extraction and synchronization of calendar data in a vendor-neutral manner, suitable for open-source publication or use with smart assistants.

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
            "name": "calendar_events",
            "endpoint": {
                "path": "/v1/calendar/events",
                "data_selector": "items",
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
- **Configuration**: The API key is stored securely and accessed via `dlt.secrets["api_key"]`. The key is included in the query parameters of each request.

## Resources

- **Resource Name**: `calendar_events`
- **Endpoint Path**: `/v1/calendar/events`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 items per request.
- **Data Selector**: The data is extracted from the `items` field in the API response.

## Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors. It retries on HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 is applied, with a maximum of 3 retries.

## Schema

- **Fields**: The schema includes fields such as `event_id`, `start_time`, `end_time`, and `summary`.
- **Types and Nullability**: Each field is defined with its type and nullability, using neutral field names to ensure compatibility across different calendar APIs.

This configuration provides a clean and generalized approach to integrating with a calendar API, abstracting away any vendor-specific details and focusing on reusable patterns for data extraction and synchronization.