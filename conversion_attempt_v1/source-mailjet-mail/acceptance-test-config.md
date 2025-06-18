```markdown
# Overview

This document provides a configuration guide for integrating with a third-party email service API using dltHub's REST API source. The integration supports data synchronization through RESTful endpoints, allowing for efficient data extraction and management.

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
            "name": "email_data",
            "endpoint": {
                "path": "/v1/emails",
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

- **Resource Name**: `email_data`
- **Endpoint Path**: `/v1/emails`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors.
- **HTTP Status Codes**: Retries are triggered for status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff is applied with a factor of 2, up to a maximum of 3 retries.

## Schema

- **Fields**: The schema includes fields such as `id`, `email_address`, `status`, and `timestamp`.
- **Types**: Fields are typed according to their expected data types (e.g., string, integer, datetime).
- **Nullability**: Fields are marked as nullable where applicable to accommodate missing data.

This configuration ensures a robust and flexible integration with the email service API, leveraging dltHub's capabilities for seamless data synchronization and management.
```