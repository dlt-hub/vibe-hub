# Overview

This document provides a configuration example for integrating with a third-party logistics API using the dltHub REST API source. The integration allows for the retrieval of various resources such as addresses, parcels, custom items, accounts, carrier accounts, and shipments. The API supports both full and incremental synchronization of data.

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
            "location": "header"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "addresses",
            "endpoint": {
                "path": "/v2/addresses",
                "data_selector": "results",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "parcels",
            "endpoint": {
                "path": "/parcels",
                "data_selector": "results",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "results",
                    "limit": 100
                }
            }
        },
        {
            "name": "custom_items",
            "endpoint": {
                "path": "/customs/items",
                "data_selector": "results",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "results",
                    "limit": 100
                }
            }
        },
        {
            "name": "accounts",
            "endpoint": {
                "path": "/shippo-accounts",
                "data_selector": "results",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "results",
                    "limit": 100
                }
            }
        },
        {
            "name": "carrier_accounts",
            "endpoint": {
                "path": "/carrier_accounts",
                "data_selector": "results",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "results",
                    "limit": 100
                }
            }
        },
        {
            "name": "shipments",
            "endpoint": {
                "path": "/shipments",
                "data_selector": "results",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "results",
                    "limit": 100
                },
                "incremental_sync": {
                    "type": "datetime",
                    "cursor_field": "object_updated",
                    "start_datetime": dlt.secrets["start_date"],
                    "datetime_format": "%Y-%m-%dT%H:%M:%SZ"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: 
  - `api_key`: The API key used for authentication, stored securely in dlt secrets.

## Resources

### Addresses
- **Endpoint Path**: `/v2/addresses`
- **Pagination Strategy**: Offset
- **Data Selector**: `results`
- **Primary Key**: `id`

### Parcels
- **Endpoint Path**: `/parcels`
- **Pagination Strategy**: Page Number
- **Data Selector**: `results`
- **Primary Key**: `object_id`

### Custom Items
- **Endpoint Path**: `/customs/items`
- **Pagination Strategy**: Page Number
- **Data Selector**: `results`
- **Primary Key**: `object_id`

### Accounts
- **Endpoint Path**: `/shippo-accounts`
- **Pagination Strategy**: Page Number
- **Data Selector**: `results`
- **Primary Key**: `object_id`

### Carrier Accounts
- **Endpoint Path**: `/carrier_accounts`
- **Pagination Strategy**: Page Number
- **Data Selector**: `results`
- **Primary Key**: `object_id`

### Shipments
- **Endpoint Path**: `/shipments`
- **Pagination Strategy**: Page Number
- **Data Selector**: `results`
- **Primary Key**: `object_id`
- **Incremental Sync**: Based on `object_updated` field

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

The schema for each resource is defined with neutral field names and types, ensuring compatibility with various data structures. Each resource's schema includes fields such as `id`, `object_id`, and `object_updated`, with appropriate data types and nullability.