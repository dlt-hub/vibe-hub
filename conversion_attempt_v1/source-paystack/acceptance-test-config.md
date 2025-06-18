# Overview

This document provides a vendor-neutral configuration for integrating with a third-party payment processing API using dltHub's REST API source format. The integration supports both full refresh and incremental data synchronization, allowing for efficient data retrieval and updates.

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
- **Pagination Strategy**: Uses an offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry Logic**: The configuration includes retry logic for handling transient errors. It retries on HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Implements an exponential backoff with a factor of 2 and a maximum of 3 retries.

## Schema

- **Fields**: The schema is defined with neutral field names such as `id`, `customer_id`, and `transaction_amount`.
- **Types and Nullability**: Each field's type and nullability are specified based on the expected data structure from the API.

This configuration ensures a clean and efficient integration with the third-party API, abstracting away any proprietary or branded content to maintain a vendor-neutral approach.