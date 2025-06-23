# Overview

This document provides a configuration guide for integrating with a third-party email marketing API using dltHub's REST API source format. The integration allows for the retrieval of campaign and list data through a RESTful interface, supporting pagination and API key-based authentication.

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
            "name": "campaigns",
            "endpoint": {
                "path": "/v1/campaigns",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 50,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "lists",
            "endpoint": {
                "path": "/v1/lists",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit": 50,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Configuration**: The API key is required and should be stored securely using `dlt.secrets["api_key"]`. It is included in the query parameters of each request.

## Resources

### Campaigns

- **Resource Name**: `campaigns`
- **Endpoint Path**: `/v1/campaigns`
- **HTTP Method**: `GET`
- **Pagination Strategy**: Page number-based pagination with parameters `page` and `limit`.
- **Data Selector**: `data`

### Lists

- **Resource Name**: `lists`
- **Endpoint Path**: `/v1/lists`
- **HTTP Method**: `GET`
- **Pagination Strategy**: Page number-based pagination with parameters `page` and `limit`.
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504 with a maximum of 3 retries and an exponential backoff factor of 2.

## Schema

### Campaigns

- **Fields**:
  - `created_at`: string, ISO 8601 format
  - `from`: object containing `email_address` (string) and `name` (string)
  - `id`: string
  - `name`: string or null
  - `sent_at`: string, ISO 8601 format
  - `status`: string (DRAFT/SENDING/SENT/ERROR)
  - `subject`: string or null
  - `to`: array of strings

### Lists

- **Fields**:
  - `counts`: object containing `pending` (integer), `subscribed` (integer), `unsubscribed` (integer)
  - `created_at`: string, ISO 8601 format
  - `double_opt_in`: boolean
  - `fields`: array of objects with `type` (string), `fallback` (null), `label` (string), `tag` (string)
  - `id`: string
  - `name`: string

This configuration provides a structured approach to accessing and managing data from a third-party email marketing API, ensuring compatibility with dltHub's REST API source format.