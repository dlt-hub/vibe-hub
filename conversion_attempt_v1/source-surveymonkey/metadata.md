# Overview

This document provides a vendor-neutral configuration for integrating with a third-party survey API using dltHub's REST API source format. The integration supports data synchronization from the API, allowing for efficient data extraction and management.

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
            "name": "survey_data",
            "endpoint": {
                "path": "/v1/surveys",
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
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access your API key.

# Resources

- **Resource Name**: `survey_data`
- **Endpoint Path**: `/v1/surveys`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: `data`

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Use an exponential backoff factor of 2 with a maximum of 3 retries.

# Schema

- **Fields**: Use neutral field names such as `survey_id`, `response_id`, etc.
- **Types**: Ensure data types are consistent with the API's response structure.
- **Nullability**: Define fields as nullable based on the API's data model.

This configuration provides a clean and generalized approach to integrating with a survey API, ensuring compatibility with dltHub's REST API source format while abstracting away any proprietary or branded content.