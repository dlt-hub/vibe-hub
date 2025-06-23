# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports various data resources, each with specific endpoints and pagination strategies. Authentication is handled via an API key, and error handling includes retry logic for certain HTTP status codes.

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
            "location": "header"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "contacts",
            "endpoint": {
                "path": "/v1/contacts",
                "data_selector": "contacts",
                "paginator": {
                    "type": "cursor",
                    "cursor_value": '{{ response.get("meta", {}).get("nextPageUrl", {}) }}',
                    "stop_condition": '{{ not response.get("meta", {}).get("nextPageUrl", {}) }}'
                }
            }
        },
        {
            "name": "payments",
            "endpoint": {
                "path": "/v1/payments",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "form_submissions",
            "endpoint": {
                "path": "/v1/form-submissions",
                "data_selector": "submissions",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 100
                }
            }
        },
        {
            "name": "custom_fields",
            "endpoint": {
                "path": "/v1/customfields",
                "data_selector": "customFields"
            }
        },
        {
            "name": "transactions",
            "endpoint": {
                "path": "/v1/transactions",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "invoices",
            "endpoint": {
                "path": "/v1/invoices",
                "data_selector": "invoices",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "opportunities",
            "endpoint": {
                "path": "/v1/opportunities",
                "data_selector": "opportunities",
                "paginator": {
                    "type": "cursor",
                    "cursor_value": '{{ response.get("meta", {}).get("nextPageUrl", {}) }}',
                    "stop_condition": '{{ not response.get("meta", {}).get("nextPageUrl", {}) }}'
                }
            }
        },
        {
            "name": "pipelines",
            "endpoint": {
                "path": "/v1/pipelines",
                "data_selector": "pipelines"
            }
        },
        {
            "name": "subscriptions",
            "endpoint": {
                "path": "/v1/subscriptions",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "orders",
            "endpoint": {
                "path": "/v1/orders",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "order",
            "endpoint": {
                "path": "/v1/orderId",
                "data_selector": []
            }
        },
        {
            "name": "contact_search",
            "endpoint": {
                "path": "/v1/contactSearch",
                "data_selector": "contacts",
                "paginator": {
                    "type": "cursor",
                    "cursor_value": '{{ response.get("searchAfter", {}) }}',
                    "stop_condition": '{{ not response.get("searchAfter", {}) }}'
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: API Key
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access the API key.
- **Location**: The API key is included in the request header.

# Resources

### Contacts
- **Endpoint Path**: `/v1/contacts`
- **Data Selector**: `contacts`
- **Pagination**: Cursor-based using `nextPageUrl`

### Payments
- **Endpoint Path**: `/v1/payments`
- **Data Selector**: `data`
- **Pagination**: Offset-based with `offset` parameter

### Form Submissions
- **Endpoint Path**: `/v1/form-submissions`
- **Data Selector**: `submissions`
- **Pagination**: Page number-based with `page` and `limit` parameters

### Custom Fields
- **Endpoint Path**: `/v1/customfields`
- **Data Selector**: `customFields`

### Transactions
- **Endpoint Path**: `/v1/transactions`
- **Data Selector**: `data`
- **Pagination**: Offset-based with `offset` parameter

### Invoices
- **Endpoint Path**: `/v1/invoices`
- **Data Selector**: `invoices`
- **Pagination**: Offset-based with `offset` parameter

### Opportunities
- **Endpoint Path**: `/v1/opportunities`
- **Data Selector**: `opportunities`
- **Pagination**: Cursor-based using `nextPageUrl`

### Pipelines
- **Endpoint Path**: `/v1/pipelines`
- **Data Selector**: `pipelines`

### Subscriptions
- **Endpoint Path**: `/v1/subscriptions`
- **Data Selector**: `data`
- **Pagination**: Offset-based with `offset` parameter

### Orders
- **Endpoint Path**: `/v1/orders`
- **Data Selector**: `data`
- **Pagination**: Offset-based with `offset` parameter

### Order
- **Endpoint Path**: `/v1/orderId`
- **Data Selector**: `[]`

### Contact Search
- **Endpoint Path**: `/v1/contactSearch`
- **Data Selector**: `contacts`
- **Pagination**: Cursor-based using `searchAfter`

# Error Handling

- **Retry Logic**: 
  - Retry on status codes: 429, 500, 502, 503, 504
  - Maximum retries: 3
  - Backoff strategy: Constant backoff with 5 seconds delay

# Schema

Each resource has a defined schema with fields, types, and nullability. Field names are generalized to maintain neutrality. For example, `id`, `name`, `email`, etc., are used across different resources.