# Overview

This document provides a vendor-neutral guide for integrating with a third-party financial data API using dltHub's REST API source format. The integration supports data synchronization through RESTful endpoints, allowing for efficient data retrieval and management.

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
            "name": "daily_data",
            "endpoint": {
                "path": "/v1/daily",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "monthly_data",
            "endpoint": {
                "path": "/v1/monthly",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "quote_data",
            "endpoint": {
                "path": "/v1/quote",
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
- **Configuration**: The API key is stored securely using `dlt.secrets["api_key"]` and is included in the query parameters of each request.

## Resources

### Daily Data
- **Endpoint Path**: `/v1/daily`
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: `data`

### Monthly Data
- **Endpoint Path**: `/v1/monthly`
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: `data`

### Quote Data
- **Endpoint Path**: `/v1/quote`
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors. It retries on HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff is applied with a maximum of 3 retries and a backoff factor of 2.

## Schema

The schema for each resource is defined with neutral field names and types. The primary key for each resource is `id`, and the data is merged based on this key. The schema is consistent with the data structure provided by the API and does not include any proprietary or branded field names.