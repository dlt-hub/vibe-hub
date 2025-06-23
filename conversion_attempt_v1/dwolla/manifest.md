# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various resources, each with its own endpoint and pagination strategy. The API uses OAuth2 for authentication and supports incremental synchronization based on datetime cursors.

## Configuration Example

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
            "token_url": "https://api.example.com/token"
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
                "path": "/customers",
                "data_selector": "_embedded.customers",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "_links.next.href",
                    "stop_condition": "_links.next is none"
                }
            }
        },
        {
            "name": "funding_sources",
            "endpoint": {
                "path": "/customers/{cus_id}/funding-sources",
                "data_selector": "_embedded.funding-sources",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "_links.next.href",
                    "stop_condition": "_links.next is none"
                }
            }
        },
        {
            "name": "events",
            "endpoint": {
                "path": "/events",
                "data_selector": "_embedded.events",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "_links.next.href",
                    "stop_condition": "_links.next is none"
                }
            }
        },
        {
            "name": "exchange_partners",
            "endpoint": {
                "path": "/exchange-partners",
                "data_selector": "_embedded.exchange-partners",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "_links.next.href",
                    "stop_condition": "_links.next is none"
                }
            }
        },
        {
            "name": "business_classifications",
            "endpoint": {
                "path": "/business-classifications",
                "data_selector": "_embedded.business-classifications",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "_links.next.href",
                    "stop_condition": "_links.next is none"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: OAuth2
- **Client ID**: `dlt.secrets["client_id"]`
- **Client Secret**: `dlt.secrets["client_secret"]`
- **Token URL**: `https://api.example.com/token`

## Resources

### Customers
- **Endpoint Path**: `/customers`
- **Data Selector**: `_embedded.customers`
- **Pagination**: Cursor-based using `_links.next.href`

### Funding Sources
- **Endpoint Path**: `/customers/{cus_id}/funding-sources`
- **Data Selector**: `_embedded.funding-sources`
- **Pagination**: Cursor-based using `_links.next.href`

### Events
- **Endpoint Path**: `/events`
- **Data Selector**: `_embedded.events`
- **Pagination**: Cursor-based using `_links.next.href`

### Exchange Partners
- **Endpoint Path**: `/exchange-partners`
- **Data Selector**: `_embedded.exchange-partners`
- **Pagination**: Cursor-based using `_links.next.href`

### Business Classifications
- **Endpoint Path**: `/business-classifications`
- **Data Selector**: `_embedded.business-classifications`
- **Pagination**: Cursor-based using `_links.next.href`

## Error Handling

- **Retry on Status Codes**: [429, 500, 502, 503, 504]
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

Each resource has a schema with fields such as `id`, `created`, and other relevant attributes. The schemas are designed to be flexible, allowing for additional properties and null values where applicable.