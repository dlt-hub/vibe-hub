# Overview

This document provides a guide for integrating with a third-party shipping API using a RESTful approach. The integration supports data synchronization through a REST API, allowing for efficient data retrieval and management.

## Configuration Example

Below is an example configuration for setting up the REST API source using dltHub's `rest_api_source`:

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
            "name": "shipping_data",
            "endpoint": {
                "path": "/v1/shipping",
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

The API requires authentication using an API key. The key should be included in the query parameters of each request.

- **Type**: `api_key`
- **Location**: `query`
- **Key Name**: `api_key`

## Resources

- **Resource Name**: `shipping_data`
- **Endpoint Path**: `/v1/shipping`
- **HTTP Method**: `GET` (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is located under the `data` field in the API response.

## Error Handling

The integration includes error handling mechanisms to manage common HTTP errors and ensure reliable data retrieval.

- **Retry Logic**: The system will retry requests on receiving specific HTTP status codes.
- **Retry Status Codes**: `[429, 500, 502, 503, 504]`
- **Max Retries**: `3`
- **Backoff Factor**: `2`

## Schema

The schema for the data retrieved from the API is designed to be neutral and generalized. It includes fields such as:

- **ID**: Unique identifier for each record.
- **Customer ID**: Identifier for the customer associated with the record.
- **Shipping Details**: Information about the shipping process.

This setup ensures a clean and efficient integration with the third-party shipping API, following best practices for RESTful API interactions.