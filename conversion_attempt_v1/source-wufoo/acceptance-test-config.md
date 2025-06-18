# Overview

This document provides a vendor-neutral configuration for integrating with a third-party form management API using dltHub's REST API source. The integration supports data synchronization through RESTful endpoints, allowing for efficient data extraction and management.

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
            "name": "form_entries",
            "endpoint": {
                "path": "/v1/forms",
                "data_selector": "entries",
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
- **Configuration**: The API key is stored securely and accessed via `dlt.secrets["api_key"]`.
- **Location**: The API key is included in the query parameters of each request.

## Resources

- **Resource Name**: `form_entries`
- **Endpoint Path**: `/v1/forms`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 entries per request.
- **Data Selector**: The data is extracted from the `entries` field in the API response.

## Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors.
- **HTTP Status Codes**: Retries are triggered for status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff is applied with a maximum of 3 retries and a backoff factor of 2.

## Schema

- **Fields**: The schema includes fields such as `id`, `form_name`, `submission_date`, and `customer_id`.
- **Types**: Fields are typed according to their expected data types (e.g., string, integer, date).
- **Nullability**: Fields are marked as nullable where applicable to accommodate optional data.

This configuration provides a clean and efficient way to interact with a third-party form management API, ensuring data is synchronized accurately and reliably.