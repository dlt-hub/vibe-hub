# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source. The integration supports data synchronization through RESTful endpoints, allowing for both full and incremental data refreshes.

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

## Authentication

- **Type**: `api_key`
- **Configuration**: The API key is stored securely using `dlt.secrets["api_key"]` and is included in the query parameters of each request.

## Resources

- **Resource Name**: Abstracted to a neutral name such as `resource_name`.
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: Default is `GET`.
- **Pagination Strategy**: Uses an offset-based paginator with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors, with retries on HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff is applied with a factor of 2, and a maximum of 3 retries.

## Schema

- **Fields**: The schema includes fields with neutral names such as `id`, `name`, and `value`.
- **Types and Nullability**: Each field's type and nullability are defined based on the API's response structure, ensuring compatibility with the data model.

This configuration provides a clean, vendor-neutral setup for integrating with a third-party analytics API using dltHub's REST API source, ensuring seamless data synchronization and error handling.