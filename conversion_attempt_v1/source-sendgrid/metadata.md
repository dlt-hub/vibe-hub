# Overview

This document provides a configuration guide for integrating with a third-party email service API using dltHub's REST API source. The integration allows for data extraction from the service's API endpoints, supporting various data synchronization strategies.

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
            "name": "email_stats",
            "endpoint": {
                "path": "/v1/email/stats",
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
- **Configuration**: The API key is stored securely and accessed via `dlt.secrets["api_key"]`. The key is included in the query parameters of each request.

## Resources

- **Resource Name**: `email_stats`
- **Endpoint Path**: `/v1/email/stats`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors. It retries on HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff is applied with a factor of 2, and a maximum of 3 retries.

## Schema

- **Fields**: The schema includes fields such as `id`, `email`, `status`, and `timestamp`.
- **Field Types**: All fields are defined with appropriate data types and nullability settings.
- **Neutral Field Names**: Field names are generalized to avoid any proprietary terms.

This configuration ensures a seamless integration with the third-party email service API, providing robust data extraction capabilities while adhering to dltHub's standards for REST API sources.