# Overview

This document provides a vendor-neutral configuration for integrating with a third-party email service API using dltHub's REST API source format. The integration supports data synchronization from various resources provided by the API, such as bounces, messages, delivery statistics, servers, and domains.

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
            "name": "bounces",
            "endpoint": {
                "path": "/v1/bounces",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "messages",
            "endpoint": {
                "path": "/v1/messages",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "deliverystats",
            "endpoint": {
                "path": "/v1/deliverystats",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "servers",
            "endpoint": {
                "path": "/v1/servers",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "domains",
            "endpoint": {
                "path": "/v1/domains",
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
- **Configuration**: The API key is stored securely and accessed via `dlt.secrets["api_key"]`.
- **Location**: The API key is included in the query parameters of each request.

## Resources

### Bounces
- **Endpoint Path**: `/v1/bounces`
- **Pagination**: Offset-based with a limit of 100 records per request.
- **Data Selector**: `data`

### Messages
- **Endpoint Path**: `/v1/messages`
- **Pagination**: Offset-based with a limit of 100 records per request.
- **Data Selector**: `data`

### Delivery Statistics
- **Endpoint Path**: `/v1/deliverystats`
- **Pagination**: Offset-based with a limit of 100 records per request.
- **Data Selector**: `data`

### Servers
- **Endpoint Path**: `/v1/servers`
- **Pagination**: Offset-based with a limit of 100 records per request.
- **Data Selector**: `data`

### Domains
- **Endpoint Path**: `/v1/domains`
- **Pagination**: Offset-based with a limit of 100 records per request.
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: The integration will retry requests on encountering HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3 attempts
- **Backoff Factor**: Exponential backoff with a factor of 2

## Schema

- **Fields**: Each resource will have a primary key field named `id`.
- **Types and Nullability**: The schema will be inferred from the API response, ensuring compatibility with the data structure provided by the API.

This configuration ensures a robust and flexible integration with the third-party email service API, allowing for efficient data synchronization and error handling.