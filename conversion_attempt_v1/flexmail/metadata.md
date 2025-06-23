# Overview

This document provides a guide for integrating with a third-party email marketing API using a RESTful approach. The integration supports data synchronization from the API, allowing for efficient data retrieval and management.

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
            "name": "email_campaigns",
            "endpoint": {
                "path": "/v1/email_campaigns",
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
- **Configuration**: The API key is stored securely and accessed via `dlt.secrets["api_key"]`. The key is included in the query parameters of each request.

# Resources

- **Resource Name**: `email_campaigns`
- **Endpoint Path**: `/v1/email_campaigns`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

# Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors. It retries on HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff is applied with a factor of 2, and a maximum of 3 retries.

# Schema

- **Fields**: The schema includes fields such as `id`, `name`, `status`, and `created_at`. These fields are abstracted to ensure neutrality and avoid proprietary terms.
- **Types and Nullability**: Each field's type and nullability are defined based on the API's response structure, ensuring accurate data representation.

This guide provides a clean, vendor-neutral approach to integrating with a third-party email marketing API, ensuring compatibility with dltHub's REST API source format.