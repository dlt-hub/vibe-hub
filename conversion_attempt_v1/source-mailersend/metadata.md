# Overview

This document provides a vendor-neutral configuration for integrating with a third-party email service API using dltHub's REST API source format. The integration supports data synchronization from the API, allowing for efficient data extraction and processing.

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
            "name": "email_service_data",
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

- **Type**: `api_key`
- **Configuration**: The API key is stored securely using `dlt.secrets["api_key"]` and is included in the query parameters of each request.

# Resources

- **Resource Name**: `email_service_data`
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

# Error Handling

- **Retry Logic**: The configuration includes retry logic for handling transient errors. The system will retry requests on specific HTTP status codes such as 429 (Too Many Requests) and 5xx server errors.
- **Backoff Strategy**: Exponential backoff is applied with a maximum of 3 retries and a backoff factor of 2.

# Schema

- **Fields**: The schema includes fields such as `id`, `name`, `status`, etc., with appropriate data types and nullability settings.
- **Field Names**: Use neutral field names like `customer_id` instead of branded identifiers.

This configuration ensures a clean and efficient integration with the third-party email service API, following dltHub's best practices for REST API sources.