# Overview

This document provides a vendor-neutral guide for integrating with a third-party email marketing API using dltHub's REST API source format. The integration supports full data synchronization from the API, utilizing a RESTful approach to access and manage resources.

## Configuration Example

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

## Authentication

- **Type**: `api_key`
- **Configuration**: The API key is stored securely and accessed via `dlt.secrets["api_key"]`.
- **Location**: The API key is included in the query parameters of each request.

## Resources

- **Resource Name**: `email_campaigns`
- **Endpoint Path**: `/v1/email_campaigns`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors.
- **HTTP Status Codes**: Retries are triggered for status codes 429 (Too Many Requests), 500 (Internal Server Error), 502 (Bad Gateway), 503 (Service Unavailable), and 504 (Gateway Timeout).
- **Backoff Strategy**: Exponential backoff is applied with a maximum of 3 retries and a backoff factor of 2.

## Schema

- **Fields**: The schema includes fields such as `id`, `name`, `status`, and `created_at`.
- **Types**: Fields are typed according to their data (e.g., string, integer, boolean).
- **Nullability**: Fields may be nullable depending on the API's response structure.

This guide ensures a clean and generalized approach to integrating with a third-party email marketing API, following dltHub's REST API source format. All parameters and configurations are derived directly from the provided YAML, ensuring no proprietary or brand-specific content is included.