# Overview

This document provides a configuration example for integrating with a third-party API that supports various resources such as orders, products, funding sources, and more. The integration allows for automated data synchronization and retrieval using a REST API. The API supports multiple endpoints and resources, each with specific configurations for pagination, authentication, and data extraction.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["api_key"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "orders",
            "endpoint": {
                "path": "/v2/orders",
                "data_selector": "orders",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "products",
            "endpoint": {
                "path": "/v2/products",
                "data_selector": "products",
                "paginator": {
                    "type": "offset",
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "funding_sources",
            "endpoint": {
                "path": "/v2/funding_sources",
                "data_selector": "funding_sources",
                "paginator": {
                    "type": "offset",
                    "limit": 10,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "account_members",
            "endpoint": {
                "path": "/v2/members",
                "data_selector": "members",
                "paginator": {
                    "type": "offset",
                    "limit": 10,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "campaigns",
            "endpoint": {
                "path": "/v2/campaigns",
                "data_selector": "campaigns",
                "paginator": {
                    "type": "offset",
                    "limit": 10,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "organizations",
            "endpoint": {
                "path": "/v2/organizations",
                "data_selector": "organizations",
                "paginator": {
                    "type": "offset",
                    "limit": 10,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "balance_transactions",
            "endpoint": {
                "path": "/v2/balance_transactions",
                "data_selector": "transactions",
                "paginator": {
                    "type": "offset",
                    "limit": 10,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "rewards",
            "endpoint": {
                "path": "/v2/rewards",
                "data_selector": "rewards",
                "paginator": {
                    "type": "offset",
                    "limit": 10,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "members",
            "endpoint": {
                "path": "/v2/members",
                "data_selector": "members",
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
                "path": "/v2/invoices",
                "data_selector": "invoices",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Bearer
- **Token**: Retrieved from `dlt.secrets["api_key"]`

## Resources

### Orders
- **Endpoint Path**: `/v2/orders`
- **Data Selector**: `orders`
- **Pagination**: Offset with `limit` of 100 and `offset_param` as `offset`

### Products
- **Endpoint Path**: `/v2/products`
- **Data Selector**: `products`
- **Pagination**: Offset with `offset_param` as `offset`

### Funding Sources
- **Endpoint Path**: `/v2/funding_sources`
- **Data Selector**: `funding_sources`
- **Pagination**: Offset with `limit` of 10 and `offset_param` as `offset`

### Account Members
- **Endpoint Path**: `/v2/members`
- **Data Selector**: `members`
- **Pagination**: Offset with `limit` of 10 and `offset_param` as `offset`

### Campaigns
- **Endpoint Path**: `/v2/campaigns`
- **Data Selector**: `campaigns`
- **Pagination**: Offset with `limit` of 10 and `offset_param` as `offset`

### Organizations
- **Endpoint Path**: `/v2/organizations`
- **Data Selector**: `organizations`
- **Pagination**: Offset with `limit` of 10 and `offset_param` as `offset`

### Balance Transactions
- **Endpoint Path**: `/v2/balance_transactions`
- **Data Selector**: `transactions`
- **Pagination**: Offset with `limit` of 10 and `offset_param` as `offset`

### Rewards
- **Endpoint Path**: `/v2/rewards`
- **Data Selector**: `rewards`
- **Pagination**: Offset with `limit` of 10 and `offset_param` as `offset`

### Members
- **Endpoint Path**: `/v2/members`
- **Data Selector**: `members`
- **Pagination**: Offset with `limit` of 100 and `offset_param` as `offset`

### Invoices
- **Endpoint Path**: `/v2/invoices`
- **Data Selector**: `invoices`
- **Pagination**: Offset with `limit` of 100 and `offset_param` as `offset`

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Use exponential backoff with a factor of 2 for retries.
- **Max Retries**: Set a maximum of 3 retries for failed requests.

## Schema

The schema for each resource is defined with fields, types, and nullability. Field names are generalized to ensure neutrality and compatibility with various data models.