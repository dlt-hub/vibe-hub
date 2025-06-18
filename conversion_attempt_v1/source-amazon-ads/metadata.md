# Overview

This document provides a configuration guide for integrating with a third-party advertising API using dltHub's REST API source. The integration allows for the extraction of advertising data through a series of API endpoints, supporting various reporting and data retrieval functionalities.

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
            "name": "advertising_profiles",
            "endpoint": {
                "path": "/v1/profiles",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "advertising_reports",
            "endpoint": {
                "path": "/v1/reports",
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

### Advertising Profiles

- **Resource Name**: `advertising_profiles`
- **Endpoint Path**: `/v1/profiles`
- **HTTP Method**: GET
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

### Advertising Reports

- **Resource Name**: `advertising_reports`
- **Endpoint Path**: `/v1/reports`
- **HTTP Method**: GET
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors. It retries on HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 is applied, with a maximum of 3 retries.

## Schema

- **Fields**: The schema includes fields such as `id`, `name`, and `status`, which are common across advertising profiles and reports.
- **Types**: Fields are typed according to their expected data types, such as strings for names and integers for IDs.
- **Nullability**: Fields may be nullable depending on the API's response structure.

This configuration provides a robust framework for integrating with a third-party advertising API, ensuring data is retrieved efficiently and securely.