# Overview

This document provides a configuration guide for integrating with a third-party ticketing API using dltHub's REST API source. The integration allows for seamless data extraction from the API, supporting various data synchronization methods.

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
            "name": "ticket_data",
            "endpoint": {
                "path": "/v1/tickets",
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

- **Type**: API Key
- **Configuration**: The API key should be stored securely and accessed via `dlt.secrets["api_key"]`. The key is included in the query parameters of each request.

# Resources

- **Resource Name**: ticket_data
- **Endpoint Path**: `/v1/tickets`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the "data" field in the API response.

# Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors. It retries on HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 is applied, with a maximum of 3 retries.

# Schema

- **Fields**: The schema includes fields such as `id`, `event_name`, `ticket_type`, and `customer_id`.
- **Types**: Ensure all fields are typed appropriately, e.g., `id` as integer, `event_name` as string.
- **Nullability**: Define which fields can be null based on the API's response structure.

This configuration provides a clean and vendor-neutral setup for integrating with a ticketing API using dltHub's REST API source, ensuring a robust and flexible data extraction process.