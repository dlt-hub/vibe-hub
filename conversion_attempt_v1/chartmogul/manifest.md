# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports multiple data resources, each with its own endpoint and pagination strategy. The API requires basic authentication using an API key.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["api_key"],
            "password": dlt.secrets["api_key"]
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
                "data_selector": "entries",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 200
                }
            }
        },
        {
            "name": "activities",
            "endpoint": {
                "path": "/v1/activities",
                "data_selector": "entries",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "start-after",
                    "limit_param": "per_page",
                    "limit": 200
                }
            }
        },
        {
            "name": "customer_daily_count",
            "endpoint": {
                "path": "/v1/metrics/customer-count",
                "data_selector": "entries"
            }
        },
        {
            "name": "customer_weekly_count",
            "endpoint": {
                "path": "/v1/metrics/customer-count",
                "data_selector": "entries"
            }
        },
        {
            "name": "customer_monthly_count",
            "endpoint": {
                "path": "/v1/metrics/customer-count",
                "data_selector": "entries"
            }
        },
        {
            "name": "customer_quarterly_count",
            "endpoint": {
                "path": "/v1/metrics/customer-count",
                "data_selector": "entries"
            }
        }
    ]
})
```

# Authentication

- **Type**: Basic Authentication
- **Secrets**: 
  - `api_key`: The API key used for both the username and password fields.

# Resources

### Customers
- **Endpoint Path**: `/v1/customers`
- **Pagination Strategy**: Page Number
  - **Page Parameter**: `page`
  - **Limit Parameter**: `per_page`
  - **Limit**: 200
- **Data Selector**: `entries`

### Activities
- **Endpoint Path**: `/v1/activities`
- **Pagination Strategy**: Cursor
  - **Cursor Parameter**: `start-after`
  - **Limit Parameter**: `per_page`
  - **Limit**: 200
- **Data Selector**: `entries`

### Customer Daily Count
- **Endpoint Path**: `/v1/metrics/customer-count`
- **Data Selector**: `entries`

### Customer Weekly Count
- **Endpoint Path**: `/v1/metrics/customer-count`
- **Data Selector**: `entries`

### Customer Monthly Count
- **Endpoint Path**: `/v1/metrics/customer-count`
- **Data Selector**: `entries`

### Customer Quarterly Count
- **Endpoint Path**: `/v1/metrics/customer-count`
- **Data Selector**: `entries`

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

The schema for each resource is defined with neutral field names and types. Here is an example for the `customers` resource:

- **id**: integer
- **name**: string or null
- **email**: string or null
- **country**: string or null
- **city**: string or null
- **state**: string or null
- **zip**: string or null
- **uuid**: string

Each resource's schema is designed to accommodate the data structure returned by the API, ensuring compatibility and ease of integration.