# Overview

This document provides a guide for integrating with a third-party survey API using a RESTful approach. The integration supports various data synchronization methods, including full refresh and incremental updates, ensuring that data is consistently and accurately retrieved from the API.

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
- **Configuration**: The API key is stored securely and accessed via `dlt.secrets["api_key"]`. The key is included in the query parameters of each request to authenticate API calls.

## Resources

- **Resource Name**: `survey_data`
- **Endpoint Path**: `/v1/surveys`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination is used, with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors. It retries on HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff is applied with a factor of 2, and a maximum of 3 retries is attempted for recoverable errors.

## Schema

- **Fields**: The schema includes fields such as `id`, `survey_name`, `response_count`, etc.
- **Types**: Each field is defined with a specific data type, such as integer, string, or boolean.
- **Nullability**: Fields are marked as nullable or non-nullable based on the API's data model.

This configuration ensures a robust and flexible integration with the survey API, allowing for efficient data extraction and synchronization.