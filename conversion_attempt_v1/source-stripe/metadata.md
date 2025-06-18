# Overview

This document provides a configuration guide for integrating with a third-party payment processing API using dltHub's REST API source. The integration supports data synchronization from various resources such as customers, invoices, charges, subscriptions, and refunds. The API allows for incremental data syncs, ensuring that both newly created and modified data are captured.

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
            "name": "customers",
            "endpoint": {
                "path": "/v1/customers",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "invoices",
            "endpoint": {
                "path": "/v1/invoices",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "charges",
            "endpoint": {
                "path": "/v1/charges",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "subscriptions",
            "endpoint": {
                "path": "/v1/subscriptions",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "refunds",
            "endpoint": {
                "path": "/v1/refunds",
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

The API uses an API key for authentication. The key should be stored securely and referenced in the configuration as shown in the example. The API key is included in the query parameters of each request.

# Resources

- **Customers**: Access customer data with pagination support.
- **Invoices**: Retrieve invoice details with pagination.
- **Charges**: Fetch charge information with pagination.
- **Subscriptions**: Obtain subscription data with pagination.
- **Refunds**: Access refund details with pagination.

Each resource uses an offset-based pagination strategy, allowing for efficient data retrieval in batches.

# Error Handling

The integration includes error handling mechanisms to manage common HTTP errors. The configuration supports retry logic with exponential backoff for status codes such as 429 (Too Many Requests) and 5xx server errors. The maximum number of retries and backoff factor can be adjusted as needed.

```python
"endpoint": {
    "path": "/v1/resource",
    "data_selector": "data",
    "error_handler": {
        "retry_on_status_codes": [429, 500, 502, 503, 504],
        "max_retries": 3,
        "backoff_factor": 2
    }
}
```

# Schema

The schema for each resource includes fields such as `id`, `created_at`, `updated_at`, and other relevant data points. Field names are generalized to ensure compatibility with various data models. The schema supports incremental updates by tracking changes to both newly created and modified records.