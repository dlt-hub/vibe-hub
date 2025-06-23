# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source format. The integration supports data synchronization from various resources, utilizing an API key for authentication and a page number-based pagination strategy.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v1/",
        "auth": {
            "type": "api_key",
            "name": "Authorization",
            "api_key": dlt.secrets["api_key"],
            "location": "header"
        }
    },
    "resource_defaults": {
        "primary_key": "_id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "billing_rates",
            "endpoint": {
                "path": "/v1/billingRate",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page"
                }
            }
        },
        {
            "name": "bookings",
            "endpoint": {
                "path": "/v1/booking",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page"
                }
            }
        },
        {
            "name": "clients",
            "endpoint": {
                "path": "/v1/client",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page"
                }
            }
        },
        {
            "name": "events",
            "endpoint": {
                "path": "/v1/event",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page"
                }
            }
        },
        {
            "name": "holidays",
            "endpoint": {
                "path": "/v1/holiday",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page"
                }
            }
        },
        {
            "name": "projects",
            "endpoint": {
                "path": "/v1/project",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page"
                }
            }
        },
        {
            "name": "resources",
            "endpoint": {
                "path": "/v1/resource",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: `api_key`
- **Header Name**: `Authorization`
- **API Key**: Stored securely in `dlt.secrets["api_key"]`

# Resources

Each resource is configured with a specific endpoint path and uses a page number-based pagination strategy. The data selector is set to "data" for extracting records.

- **Resource Name**: Abstracted from the original configuration
- **Endpoint Path**: `/v1/{resource_name}`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Page number with a limit of 100 records per page
- **Data Selector**: "data"

# Error Handling

The configuration does not explicitly define error handling strategies. It is recommended to implement retry logic for common HTTP status codes such as 429, 500, 502, 503, and 504, with a maximum of 3 retries and an exponential backoff factor of 2.

# Schema

The schema for each resource is defined with neutral field names and types, allowing for flexibility in data integration. Fields include identifiers, timestamps, and other relevant attributes, with support for nullable values.

- **Field Names**: Use neutral terms like `_id`, `createdDate`, `updatedDate`, etc.
- **Types**: String, number, boolean, object, array
- **Nullability**: Supported for all fields

This configuration provides a clean, vendor-neutral setup for integrating with a third-party analytics API using dltHub's REST API source format.