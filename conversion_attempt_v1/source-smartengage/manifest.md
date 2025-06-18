# Overview

This document provides a configuration guide for integrating with a third-party analytics API using the dltHub REST API source format. The integration supports multiple resources, each with its own endpoint and schema. The API uses bearer token authentication and supports partitioned data retrieval based on a parent-child relationship between resources.

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
            "name": "avatars",
            "endpoint": {
                "path": "/v1/avatars/list",
                "data_selector": "data"
            }
        },
        {
            "name": "tags",
            "endpoint": {
                "path": "/v1/tags/list",
                "data_selector": "data",
                "params": {
                    "avatar_id": "{{ stream_slice.avatar_id }}"
                }
            }
        },
        {
            "name": "custom_fields",
            "endpoint": {
                "path": "/v1/customfields/list",
                "data_selector": "data",
                "params": {
                    "avatar_id": "{{ stream_slice.avatar_id }}"
                }
            }
        },
        {
            "name": "sequences",
            "endpoint": {
                "path": "/v1/sequences/list",
                "data_selector": "data",
                "params": {
                    "avatar_id": "{{ stream_slice.avatar_id }}"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Bearer Token
- **Token**: Retrieved from `dlt.secrets["api_key"]`

## Resources

### Avatars
- **Endpoint Path**: `/v1/avatars/list`
- **Primary Key**: `avatar_id`
- **Data Selector**: `data`

### Tags
- **Endpoint Path**: `/v1/tags/list`
- **Primary Key**: `tag_id`
- **Data Selector**: `data`
- **Parameters**: `avatar_id` from parent resource

### Custom Fields
- **Endpoint Path**: `/v1/customfields/list`
- **Primary Key**: `custom_field_id`
- **Data Selector**: `data`
- **Parameters**: `avatar_id` from parent resource

### Sequences
- **Endpoint Path**: `/v1/sequences/list`
- **Primary Key**: `sequence_id`
- **Data Selector**: `data`
- **Parameters**: `avatar_id` from parent resource

## Error Handling

- **Retry Logic**: Implement retry on specific HTTP status codes (e.g., 429, 500, 502, 503, 504)
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

### Avatars
- **Fields**:
  - `avatar_id`: string, required
  - `brand_image`: string or null
  - `brand_name`: string or null
  - `facebook_page_id`: string or null
  - `user_role`: string or null

### Tags
- **Fields**:
  - `tag_id`: string, required
  - `tag_name`: string or null

### Custom Fields
- **Fields**:
  - `custom_field_id`: string, required
  - `custom_field_name`: string or null

### Sequences
- **Fields**:
  - `sequence_id`: string, required
  - `sequence_name`: string or null

This configuration provides a clean, vendor-neutral setup for accessing and synchronizing data from a third-party analytics API using dltHub's REST API source format.