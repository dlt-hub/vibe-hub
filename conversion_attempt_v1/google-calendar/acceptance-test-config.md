# Overview

This document provides a vendor-neutral configuration for integrating with a third-party calendar API using dltHub's REST API source format. The integration supports data synchronization through RESTful endpoints, allowing for efficient data retrieval and management.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "oauth2",
            "client_id": dlt.secrets["client_id"],
            "client_secret": dlt.secrets["client_secret"],
            "token_url": "https://api.example.com/oauth/token"
        }
    },
    "resource_defaults": {
        "primary_key": "event_id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "calendar_events",
            "endpoint": {
                "path": "/v1/events",
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

- **Type**: OAuth2
- **Secrets**: 
  - `client_id`: The client identifier for the OAuth2 application.
  - `client_secret`: The client secret for the OAuth2 application.
  - `token_url`: The URL to obtain the OAuth2 token.

## Resources

- **Resource Name**: calendar_events
- **Endpoint Path**: `/v1/events`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 items per request.
- **Data Selector**: The data is located under the `items` field in the response.

## Error Handling

- **Retry Logic**: 
  - Retry on HTTP status codes: 429, 500, 502, 503, 504
  - Maximum retries: 3
  - Backoff factor: 2

## Schema

- **Fields**:
  - `event_id`: Unique identifier for each event.
  - `start_time`: The start time of the event.
  - `end_time`: The end time of the event.
  - `summary`: A brief description of the event.
- **Types**: Ensure all fields are appropriately typed and nullable as per the API's response schema.

This configuration provides a clean and generalized setup for accessing a calendar API, abstracting away any proprietary or branded content. It is designed to be reusable and adaptable for various third-party RESTful API integrations.