# Overview

This document provides a configuration guide for integrating with a generic third-party database API using dltHub's REST API source. The integration supports data synchronization from the API to your data warehouse, utilizing a RESTful approach with customizable authentication and pagination strategies.

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

- **Retry Logic**: The integration includes retry logic for handling transient errors, with a backoff strategy.
- **HTTP Status Codes**: Retries are triggered for status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3 attempts are made before failing.
- **Backoff Factor**: Exponential backoff is applied with a factor of 2.

# Schema

- **Fields**: The schema is defined with neutral field names, such as `id`, `name`, and `value`.
- **Types and Nullability**: Each field's type and nullability are configured based on the API's response structure.

This configuration provides a clean, vendor-neutral setup for integrating with a third-party database API using dltHub's REST API source. It abstracts away any proprietary or branded content, ensuring a flexible and reusable integration pattern.