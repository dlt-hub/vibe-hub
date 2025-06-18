# Overview

This document provides a vendor-neutral configuration for integrating with a third-party help desk API. The integration supports data synchronization from the API to your data platform using a RESTful approach. The configuration is designed to be compatible with dltHub's REST API source format, ensuring a clean and efficient data extraction process.

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
            "name": "help_desk_tickets",
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
- **Configuration**: The API key is stored securely and accessed via `dlt.secrets["api_key"]`. The key is included in the query parameters of each request to authenticate API calls.

# Resources

- **Resource Name**: help_desk_tickets
- **Endpoint Path**: `/v1/tickets`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the "data" field in the API response.

# Error Handling

- **Retry Logic**: The configuration includes retry logic for handling transient errors. The system will retry requests on receiving HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff is applied with a factor of 2, and a maximum of 3 retries is allowed.

# Schema

- **Fields**: The schema includes fields such as `id`, `subject`, `status`, and `created_at`. These fields are mapped to the corresponding data points in the API response.
- **Field Types**: The fields are typed according to their expected data types (e.g., string, integer, datetime).
- **Nullability**: Fields are marked as nullable based on the API's data model, ensuring compatibility with varying data completeness.

This configuration provides a robust framework for integrating with a help desk API, abstracting away vendor-specific details and focusing on the essential patterns for data extraction and synchronization.