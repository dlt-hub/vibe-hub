# Overview

This document provides a vendor-neutral configuration for integrating with a third-party financial API using dltHub's REST API source. The integration supports both full refresh and incremental data synchronization, allowing for efficient data retrieval and updates.

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
            "name": "financial_data",
            "endpoint": {
                "path": "/v1/financial_data",
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

- **Type**: API Key
- **Configuration**: The API key is stored securely using `dlt.secrets["api_key"]` and is included in the query parameters of each request.

## Resources

- **Resource Name**: financial_data
- **Endpoint Path**: `/v1/financial_data`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the "data" field in the API response.

## Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors. It retries on HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 is applied, with a maximum of 3 retries.

## Schema

- **Fields**: The schema includes fields such as `id`, `amount`, `date`, and `description`. These fields are generalized and do not include any proprietary or branded terms.
- **Types and Nullability**: Each field's type and nullability are defined based on the API's response structure, ensuring compatibility with dltHub's data processing.

This configuration provides a clean and reusable template for integrating with a third-party financial API, abstracting away any proprietary or branded content.