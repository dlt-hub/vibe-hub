# Overview

This document provides a configuration guide for integrating with a third-party analytics API using the dltHub REST API source. The integration supports data synchronization from various resources, each with its own endpoint and schema. The API uses bearer token authentication and supports cursor-based pagination for data retrieval.

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
            "name": "tables",
            "endpoint": {
                "path": "/v1/tables",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next",
                    "stop_condition": "none"
                }
            }
        },
        {
            "name": "terms",
            "endpoint": {
                "path": "/v1/terms",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next",
                    "stop_condition": "none"
                }
            }
        },
        {
            "name": "collections",
            "endpoint": {
                "path": "/v1/collections",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next",
                    "stop_condition": "none"
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

### Tables

- **Endpoint Path**: `/v1/tables`
- **Data Selector**: `results`
- **Pagination Strategy**: Cursor-based
  - **Cursor Parameter**: `next`
  - **Stop Condition**: When `next` is `none`

### Terms

- **Endpoint Path**: `/v1/terms`
- **Data Selector**: `results`
- **Pagination Strategy**: Cursor-based
  - **Cursor Parameter**: `next`
  - **Stop Condition**: When `next` is `none`

### Collections

- **Endpoint Path**: `/v1/collections`
- **Data Selector**: `results`
- **Pagination Strategy**: Cursor-based
  - **Cursor Parameter**: `next`
  - **Stop Condition**: When `next` is `none`

## Error Handling

- **Retry on Status Codes**: [500]
- **Action**: Fail on specified HTTP status codes

## Schema

### Tables

- **Fields**:
  - `type`: string
  - `cluster`: string
  - `columns`: array
  - `created_at`: string
  - `database`: string
  - `description`: nullable string
  - `entity_type`: string
  - `id`: string
  - `integration`: string
  - `name`: string
  - `owners`: array
  - `parent_id`: string
  - `pii`: boolean
  - `pristine`: boolean
  - `published`: boolean
  - `schema`: string
  - `tags`: array
  - `updated_at`: string
  - `verified`: boolean

### Terms

- **Fields**:
  - `collections`: array
  - `created_at`: string
  - `description`: string
  - `entity_type`: string
  - `id`: string
  - `owners`: array
  - `pristine`: boolean
  - `published`: boolean
  - `title`: string
  - `updated_at`: string
  - `viewers_delta`: object

### Collections

- **Fields**:
  - `archived`: boolean
  - `created_at`: string
  - `definition`: string
  - `description`: string
  - `entity_type`: string
  - `id`: string
  - `pii`: boolean
  - `published`: boolean
  - `title`: string
  - `updated_at`: string
  - `verified`: boolean
  - `workspace_id`: string

This configuration provides a clean and generalized setup for integrating with a third-party analytics API using dltHub's REST API source, ensuring a vendor-neutral approach.