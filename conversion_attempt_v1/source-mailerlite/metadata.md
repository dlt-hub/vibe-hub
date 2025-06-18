# Overview

This document provides a configuration guide for integrating with a third-party email marketing API using dltHub's REST API source. The integration allows for data extraction from the API, supporting various resource endpoints and authentication methods.

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
- **Backoff Strategy**: Exponential backoff with a factor of 2 is applied, with a maximum of 3 retries.

# Schema

- **Fields**: The schema includes fields such as `id`, `name`, `status`, and other relevant attributes for email campaigns.
- **Field Types**: Fields are typed according to their expected data types, such as strings, integers, and booleans.
- **Nullability**: Fields are marked as nullable where applicable, ensuring compatibility with the API's response structure.

This configuration provides a clean and vendor-neutral setup for integrating with a third-party email marketing API using dltHub's REST API source. It abstracts away any proprietary details, focusing solely on the reusable patterns and configurations necessary for successful data extraction.