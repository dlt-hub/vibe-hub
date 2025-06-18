# Overview

This document provides a configuration guide for integrating with a third-party service desk API using dltHub's REST API source. The integration allows for data extraction from the service desk platform, supporting various data synchronization types.

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
            "name": "service_desk_tickets",
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

- **Type**: `api_key`
- **Secrets or Config Values**: Use `api_key` stored securely in `dlt.secrets`.

# Resources

- **Resource Name**: `service_desk_tickets`
- **Endpoint Path and Method**: `/v1/tickets`, default method is GET.
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

# Error Handling

- **Retry/Backoff Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504 with a maximum of 3 retries and an exponential backoff factor of 2.
- **Known HTTP Response Codes**: Handle common HTTP errors with appropriate retry mechanisms.

# Schema

- **Fields**: Use neutral field names such as `ticket_id`, `customer_id`, `status`, etc.
- **Types and Nullability**: Define field types and nullability based on the API response structure, ensuring consistency with the data model.

This configuration provides a clean, vendor-neutral setup for integrating with a service desk API using dltHub's REST API source, abstracting away any proprietary or branded content.