# Overview

This document provides a vendor-neutral guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports various synchronization types, including incremental and full refresh, allowing for flexible data retrieval from the API.

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
            "name": "resource_name",
            "endpoint": {
                "path": "/v1/resource",
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
- **Configuration**: The API key is stored securely using `dlt.secrets["api_key"]` and is included in the query parameters of each request.

# Resources

- **Resource Name**: A generic name representing the data resource.
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the "data" field in the API response.

# Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors, with a maximum of 3 retries and an exponential backoff factor of 2.
- **HTTP Status Codes**: The integration is configured to retry on status codes 429, 500, 502, 503, and 504.
- **Timeouts**: Requests are subject to a timeout to prevent indefinite waiting periods.

# Schema

- **Fields**: The schema includes fields with neutral names such as `customer_id`, `event_type`, and `timestamp`.
- **Types**: Each field has a defined data type, such as string, integer, or datetime.
- **Nullability**: Fields may be nullable, depending on the API's data structure.

This guide ensures a clean and generalized approach to integrating with a third-party analytics API, free from proprietary or branded content.