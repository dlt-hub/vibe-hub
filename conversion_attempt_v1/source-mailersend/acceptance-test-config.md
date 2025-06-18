```markdown
# Overview

This document provides a configuration guide for integrating with a third-party email service API using dltHub's REST API source. The integration supports full data synchronization from the API, allowing for efficient data extraction and management.

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
- **Configuration**: The API key is stored securely using `dlt.secrets["api_key"]` and is included in the query parameters of each request.

## Resources

- **Resource Name**: `email_data`
- **Endpoint Path**: `/v1/emails`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors. It retries on HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff is applied with a maximum of 3 retries and a backoff factor of 2.

## Schema

- **Fields**: The schema includes fields such as `id`, `email_address`, `status`, and other relevant data points.
- **Types**: Fields are typed according to their data (e.g., string, integer).
- **Nullability**: Fields are nullable based on the API's response structure.

This configuration ensures a robust and efficient integration with the third-party email service API, leveraging dltHub's capabilities for data extraction and synchronization.
```