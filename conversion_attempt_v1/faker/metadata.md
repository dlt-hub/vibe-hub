# Overview

This document provides a configuration example for integrating with a third-party API using dltHub's REST API source. The integration supports fetching data from various resources, such as users, products, and purchases, using a RESTful approach. The configuration is designed to be vendor-neutral and does not include any proprietary or branded content.

# Configuration Example

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
            "name": "users",
            "endpoint": {
                "path": "/v1/users",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "products",
            "endpoint": {
                "path": "/v1/products",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "purchases",
            "endpoint": {
                "path": "/v1/purchases",
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

# Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: The API key is stored securely using `dlt.secrets["api_key"]`. The key is included in the query parameters of each request.

# Resources

- **Resource Name**: `users`, `products`, `purchases`
- **Endpoint Path and Method**: Each resource has a specific endpoint path (e.g., `/v1/users`) and uses the GET method by default.
- **Pagination Strategy**: Uses an offset-based pagination strategy with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

# Error Handling

- **Retry/Backoff Logic**: The configuration includes retry logic for handling transient errors. It retries on HTTP status codes such as 429, 500, 502, 503, and 504, with a maximum of 3 retries and a backoff factor of 2.
- **Known HTTP Response Codes**: The integration is designed to handle common HTTP response codes and implement appropriate retry mechanisms.

# Schema

- **Fields, Types, Nullability**: The schema for each resource is defined with neutral field names. For example, the primary key is referred to as `id`. The schema is consistent with the data structure provided by the API and does not include any proprietary field names.