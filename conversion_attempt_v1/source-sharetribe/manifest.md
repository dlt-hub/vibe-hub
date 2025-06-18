# Overview

This document provides a configuration guide for integrating with a third-party marketplace API using dltHub's REST API source. The integration supports various resources such as users, marketplace, listings, transactions, events, messages, bookings, and reviews. The API uses OAuth for authentication, requiring a client ID and client secret.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "oauth2",
            "client_id": dlt.secrets["client_id"],
            "client_secret": dlt.secrets["client_secret"],
            "token_url": "https://api.example.com/v1/auth/token"
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
                "path": "/v1/resource/users/query",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "perPage",
                    "limit": 100
                }
            }
        },
        {
            "name": "marketplace",
            "endpoint": {
                "path": "/v1/resource/marketplace/show",
                "data_selector": "data"
            }
        },
        {
            "name": "listings",
            "endpoint": {
                "path": "/v1/resource/listings/query",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "perPage",
                    "limit": 100
                }
            }
        },
        {
            "name": "transactions",
            "endpoint": {
                "path": "/v1/resource/transactions/query",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "perPage",
                    "limit": 100
                }
            }
        },
        {
            "name": "events",
            "endpoint": {
                "path": "/v1/resource/events/query",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "perPage",
                    "limit": 100
                }
            }
        },
        {
            "name": "messages",
            "endpoint": {
                "path": "/v1/resource/transactions/query",
                "data_selector": "included",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "perPage",
                    "limit": 100
                }
            }
        },
        {
            "name": "bookings",
            "endpoint": {
                "path": "/v1/resource/transactions/query",
                "data_selector": "included",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "perPage",
                    "limit": 100
                }
            }
        },
        {
            "name": "reviews",
            "endpoint": {
                "path": "/v1/resource/transactions/query",
                "data_selector": "included",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "perPage",
                    "limit": 100
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: OAuth2
- **Required Secrets**:
  - `client_id`
  - `client_secret`
- **Token URL**: `https://api.example.com/v1/auth/token`

# Resources

### Users
- **Endpoint Path**: `/v1/resource/users/query`
- **Data Selector**: `data`
- **Pagination**: Page number with `page` and `perPage` parameters, limit 100

### Marketplace
- **Endpoint Path**: `/v1/resource/marketplace/show`
- **Data Selector**: `data`

### Listings
- **Endpoint Path**: `/v1/resource/listings/query`
- **Data Selector**: `data`
- **Pagination**: Page number with `page` and `perPage` parameters, limit 100

### Transactions
- **Endpoint Path**: `/v1/resource/transactions/query`
- **Data Selector**: `data`
- **Pagination**: Page number with `page` and `perPage` parameters, limit 100

### Events
- **Endpoint Path**: `/v1/resource/events/query`
- **Data Selector**: `data`
- **Pagination**: Page number with `page` and `perPage` parameters, limit 100

### Messages
- **Endpoint Path**: `/v1/resource/transactions/query`
- **Data Selector**: `included`
- **Pagination**: Page number with `page` and `perPage` parameters, limit 100

### Bookings
- **Endpoint Path**: `/v1/resource/transactions/query`
- **Data Selector**: `included`
- **Pagination**: Page number with `page` and `perPage` parameters, limit 100

### Reviews
- **Endpoint Path**: `/v1/resource/transactions/query`
- **Data Selector**: `included`
- **Pagination**: Page number with `page` and `perPage` parameters, limit 100

# Error Handling

- **Retry Logic**: Implement retry on HTTP status codes 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

Each resource has a schema with fields such as `id` and `createdAt`. The schemas are designed to be flexible, allowing for additional properties and null values where applicable. Field names are generalized to ensure neutrality.