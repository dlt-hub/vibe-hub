# Overview

This document provides a configuration guide for integrating with a third-party form management API using dltHub's REST API source. The integration allows for seamless data extraction from the API, supporting various sync types and configurations.

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
            "name": "form_data",
            "endpoint": {
                "path": "/v1/forms",
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
- **Configuration**: The API key should be stored securely and accessed via `dlt.secrets["api_key"]`. The key is included in the query parameters of each request.

# Resources

- **Resource Name**: `form_data`
- **Endpoint Path**: `/v1/forms`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

# Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors. It retries on HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 is applied, with a maximum of 3 retries.

# Schema

- **Fields**: The schema includes fields such as `id`, `form_name`, and `submission_date`. All field names are generalized and neutral.
- **Types and Nullability**: Each field's type and nullability are defined based on the API's response structure, ensuring compatibility with dltHub's data processing.

This configuration provides a clean and vendor-neutral setup for integrating with a form management API, ensuring data is extracted efficiently and reliably.