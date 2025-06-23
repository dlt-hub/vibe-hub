# Overview

This document provides a configuration guide for integrating with a third-party forms API using dltHub's REST API source. The integration supports data synchronization from the API, allowing for efficient data retrieval and management.

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
            "name": "form_responses",
            "endpoint": {
                "path": "/v1/forms/responses",
                "data_selector": "responses",
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
- **Configuration**: The API key is stored securely and accessed via `dlt.secrets["api_key"]`. It is included in the query parameters of each request.

## Resources

- **Resource Name**: `form_responses`
- **Endpoint Path**: `/v1/forms/responses`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 items per request.
- **Data Selector**: The data is extracted from the `responses` field in the API response.

## Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors. It retries on HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 is applied, with a maximum of 3 retries.

## Schema

- **Fields**: The schema includes fields such as `id`, `response_date`, and `answers`, which are extracted from the API response.
- **Field Types**: The fields are typed according to the data returned by the API, ensuring compatibility and consistency.
- **Nullability**: Fields are nullable based on the API's response structure, allowing for flexible data handling.

This configuration provides a clean and efficient way to integrate with a third-party forms API, leveraging dltHub's REST API source capabilities.