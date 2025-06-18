# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports data synchronization from the API, allowing for incremental data retrieval based on date fields.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v2",
        "auth": {
            "type": "api_key",
            "name": "access_key_id",
            "api_key": dlt.secrets["access_token"],
            "location": "header"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "calls",
            "endpoint": {
                "path": "/calls/reporting/simple",
                "data_selector": "items",
                "paginator": {
                    "type": "cursor",
                    "limit": 102,
                    "cursor_param": "page",
                    "stop_condition": "no_more_pages"
                }
            },
            "incremental_sync": {
                "cursor_field": "dateCreated",
                "start_time_param": "filters.dateCreated.from",
                "end_time_param": "filters.dateCreated.to"
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets**: 
  - `access_key_id`: The API access key ID.
  - `access_token`: The API access token.
- **Location**: Headers

## Resources

- **Resource Name**: `calls`
- **Endpoint Path**: `/calls/reporting/simple`
- **HTTP Method**: `GET`
- **Pagination Strategy**: Cursor-based pagination using a `page` parameter.
- **Data Selector**: `items`

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 and a maximum of 3 retries.

## Schema

The schema defines the structure of the data retrieved from the API. It includes fields such as:

- `id`: String, primary key
- `dateCreated`: String, date-time format
- `type`: String, max length 256
- `anonymous`: Boolean or null
- `conversationId`: String, max length 32
- `duration`: Integer or null
- `state`: String, max length 256

The schema supports additional properties and is designed to accommodate various data types, including strings, integers, booleans, and objects.