# Overview

This document provides a configuration guide for integrating with a third-party survey API using dltHub's REST API source. The integration allows for seamless data extraction from the API, supporting various sync types and configurations.

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

## Authentication

- **Type**: `api_key`
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access your API key.
- **Location**: The API key is included in the query parameters of the request.

## Resources

- **Resource Name**: `survey_data`
- **Endpoint Path**: `/v1/surveys`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors.
- **HTTP Status Codes**: Retries are triggered for status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 is applied, with a maximum of 3 retries.

## Schema

- **Fields**: The schema includes fields such as `id`, `survey_name`, and `response_count`.
- **Types**: Fields are typed according to their data (e.g., integer, string).
- **Nullability**: Fields may be nullable depending on the API's response structure.

This configuration provides a clean and vendor-neutral setup for integrating with a survey API, ensuring compatibility with dltHub's REST API source framework.