# Overview

This document provides a vendor-neutral guide for integrating with a third-party spreadsheet API using dltHub's REST API source format. The integration supports full data refreshes from the API, with a focus on retrieving data from spreadsheet resources.

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
            "name": "spreadsheet_data",
            "endpoint": {
                "path": "/v1/spreadsheet",
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

- **Resource Name**: `spreadsheet_data`
- **Endpoint Path**: `/v1/spreadsheet`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors.
- **HTTP Status Codes**: Retries are triggered for status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 is applied, with a maximum of 3 retries.

## Schema

- **Fields**: The schema includes fields such as `id`, `name`, and `value`, with types and nullability defined based on the API's response structure.
- **Field Names**: Use neutral field names like `spreadsheet_id` and `entry_value` to maintain generality.

This guide ensures a clean and generalized approach to integrating with a spreadsheet API using dltHub's REST API source, abstracting away any proprietary or branded content.