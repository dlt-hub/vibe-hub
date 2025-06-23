# Overview

This document provides a configuration guide for integrating with a third-party employment management API using dltHub's REST API source. The integration supports data synchronization from the API, allowing for efficient data extraction and processing.

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
            "name": "employment_data",
            "endpoint": {
                "path": "/v1/employment",
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

- **Resource Name**: `employment_data`
- **Endpoint Path**: `/v1/employment`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors. It retries on HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 is applied, with a maximum of 3 retries.

## Schema

- **Fields**: The schema includes fields such as `id`, `name`, `status`, and other employment-related data.
- **Field Types**: All fields are defined with appropriate data types and nullability settings.
- **Neutral Field Names**: Field names are generalized to avoid any proprietary terms.

This configuration ensures a seamless and efficient integration with the employment management API, leveraging dltHub's capabilities for data extraction and processing.