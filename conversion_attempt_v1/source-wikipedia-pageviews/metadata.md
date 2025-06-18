# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source. The integration allows for the retrieval of pageview data from a specified API endpoint. The configuration is designed to be vendor-neutral and does not include any proprietary or branded content.

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
            "name": "pageviews",
            "endpoint": {
                "path": "/v1/pageviews",
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
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access your API key.
- **Location**: The API key is included in the query parameters of the request.

## Resources

- **Resource Name**: `pageviews`
- **Endpoint Path**: `/v1/pageviews`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry Logic**: The configuration includes retry logic for handling transient errors.
- **HTTP Status Codes**: Retries are triggered for status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 and a maximum of 3 retries.

## Schema

- **Fields**: The schema includes fields such as `id`, `timestamp`, and `views`.
- **Types**: Ensure that field types are consistent with the API response.
- **Nullability**: Fields may be nullable depending on the API's data structure.

This configuration provides a clean and generalized setup for integrating with a third-party analytics API using dltHub's REST API source. It abstracts away any vendor-specific details, ensuring a flexible and reusable integration pattern.