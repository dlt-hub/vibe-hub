# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source. The integration supports various data resources, each with specific endpoints and pagination strategies. The API requires authentication, and the configuration supports both API token and OAuth2.0 methods.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "api_key",  # or "oauth2"
            "name": "api_key",
            "api_key": dlt.secrets["api_key"],
            "location": "query"  # or "header"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "limits",
            "endpoint": {
                "path": "/v1/limits",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "next",
                    "stop_condition": "no_next"
                }
            }
        },
        {
            "name": "object_types",
            "endpoint": {
                "path": "/v1/objects/types",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "next",
                    "stop_condition": "no_next"
                }
            }
        },
        {
            "name": "object_records",
            "endpoint": {
                "path": "/v1/objects/query",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "next",
                    "stop_condition": "no_next"
                }
            }
        },
        {
            "name": "object_type_policies",
            "endpoint": {
                "path": "/v1/objects/types/{type}/permissions",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "next",
                    "stop_condition": "no_next"
                }
            }
        },
        {
            "name": "relationship_types",
            "endpoint": {
                "path": "/v1/relationships/types",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "next",
                    "stop_condition": "no_next"
                }
            }
        },
        {
            "name": "relationship_records",
            "endpoint": {
                "path": "/v1/relationships/records",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "next",
                    "stop_condition": "no_next"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key` or `oauth2`
- **Secrets or Config Values**:
  - For `api_key`: `api_key`, `location`
  - For `oauth2`: `client_id`, `client_secret`, `access_token`

## Resources

### Limits
- **Endpoint Path**: `/v1/limits`
- **Pagination Strategy**: Cursor-based
- **Data Selector**: `data`

### Object Types
- **Endpoint Path**: `/v1/objects/types`
- **Pagination Strategy**: Cursor-based
- **Data Selector**: `data`

### Object Records
- **Endpoint Path**: `/v1/objects/query`
- **Pagination Strategy**: Cursor-based
- **Data Selector**: `data`

### Object Type Policies
- **Endpoint Path**: `/v1/objects/types/{type}/permissions`
- **Pagination Strategy**: Cursor-based
- **Data Selector**: `data`

### Relationship Types
- **Endpoint Path**: `/v1/relationships/types`
- **Pagination Strategy**: Cursor-based
- **Data Selector**: `data`

### Relationship Records
- **Endpoint Path**: `/v1/relationships/records`
- **Pagination Strategy**: Cursor-based
- **Data Selector**: `data`

## Error Handling

- **Retry/Backoff Logic**:
  - Retry on status codes: 429, 500, 502, 503, 504
  - Max retries: 3
  - Backoff factor: 2
- **Known HTTP Response Codes**: 429, 500, 502, 503, 504

## Schema

### Limits
- **Fields**:
  - `count`: integer or null
  - `key`: string or null
  - `limit`: integer or null

### Object Types
- **Fields**:
  - `created_at`: string or null
  - `key`: string or null
  - `schema`: object or null
  - `updated_at`: string or null

### Object Records
- **Fields**:
  - `type`: string or null
  - `attributes`: object or null
  - `created_at`: string or null
  - `external_id`: string or null
  - `id`: string or null
  - `updated_at`: string or null

### Object Type Policies
- **Fields**:
  - `object_type`: string or null
  - `rbac`: object or null

### Relationship Types
- **Fields**:
  - `created_at`: string or null
  - `key`: string or null
  - `source`: string or null
  - `target`: string or null
  - `updated_at`: string or null

### Relationship Records
- **Fields**:
  - `created_at`: string
  - `id`: string
  - `relationship_type`: string
  - `source`: string
  - `target`: string

This configuration provides a comprehensive guide to setting up a REST API source using dltHub, ensuring a clean and vendor-neutral integration.