# Overview

This document provides a configuration example for integrating with a third-party CRM API using dltHub's REST API source. The integration supports various data resources, allowing for efficient data synchronization and management.

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
            "name": "account",
            "endpoint": {
                "path": "/v1/account",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "contact",
            "endpoint": {
                "path": "/v1/contact",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "lead",
            "endpoint": {
                "path": "/v1/lead",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "opportunity",
            "endpoint": {
                "path": "/v1/opportunity",
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
- **Secrets**: Use `dlt.secrets["api_key"]` to securely manage your API key.
- **Location**: The API key is included in the query parameters of each request.

## Resources

- **Resource Names**: `account`, `contact`, `lead`, `opportunity`
- **Endpoint Paths**: `/v1/account`, `/v1/contact`, `/v1/lead`, `/v1/opportunity`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry Logic**: The integration will retry requests on specific HTTP status codes such as 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 and a maximum of 3 retries.

## Schema

- **Fields**: The schema includes fields such as `id`, `name`, `status`, etc., with types and nullability defined based on the API's response structure.
- **Field Names**: Use neutral field names like `customer_id` instead of branded identifiers.

This configuration provides a robust framework for synchronizing data from a third-party CRM API, ensuring efficient data handling and error management.