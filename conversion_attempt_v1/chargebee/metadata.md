# Overview

This document provides a configuration guide for integrating with a third-party subscription management API using dltHub's REST API source. The integration supports various resource types, allowing for data synchronization from the API to your data pipeline.

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
            "name": "subscription",
            "endpoint": {
                "path": "/v1/subscription",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "customer",
            "endpoint": {
                "path": "/v1/customer",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "invoice",
            "endpoint": {
                "path": "/v1/invoice",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        }
        // Additional resources can be added here following the same pattern
    ]
})
```

# Authentication

- **Type**: `api_key`
- **Configuration**: The API key is stored securely using `dlt.secrets["api_key"]` and is included in the query parameters of each request.

# Resources

- **Resource Names**: subscription, customer, invoice, etc.
- **Endpoint Paths**: Each resource has a specific endpoint path, such as `/v1/subscription`.
- **Pagination Strategy**: Uses an offset-based pagination strategy with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

# Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors, with a backoff strategy for HTTP status codes like 429 (Too Many Requests) and 5xx server errors.
- **Max Retries**: Configurable number of retries with exponential backoff.

# Schema

- **Fields**: The schema includes fields such as `id`, `customer_id`, `amount`, etc., with types and nullability defined based on the API's response structure.
- **Neutral Field Names**: Field names are generalized to avoid any proprietary terms.

This configuration allows for seamless integration with the API, enabling data extraction and synchronization in a vendor-neutral manner.