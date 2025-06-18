# Overview

This document provides a configuration guide for integrating with a third-party e-signature API using dltHub's REST API source. The integration allows for automated data synchronization of e-signature documents, folder structures, spaces, and webhooks, facilitating streamlined workflows and data accessibility across systems.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v1/e-signature",
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
            "name": "documents",
            "endpoint": {
                "path": "/documents",
                "data_selector": ["results", "documents"],
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "skip"
                }
            }
        },
        {
            "name": "folders",
            "endpoint": {
                "path": "/folders",
                "data_selector": ["results", "folders"]
            }
        },
        {
            "name": "spaces",
            "endpoint": {
                "path": "/spaces",
                "data_selector": ["results", "spaces"]
            }
        },
        {
            "name": "webhooks",
            "endpoint": {
                "path": "/folders/{{ stream_partition.folder_id }}/webhooks",
                "data_selector": ["results", "webhooks"]
            }
        }
    ]
})
```

## Authentication

- **Type**: Bearer
- **Token**: Retrieved from `dlt.secrets["api_key"]`

## Resources

### Documents
- **Endpoint Path**: `/documents`
- **Pagination Strategy**: Offset
  - **Limit**: 100
  - **Offset Parameter**: `skip`
- **Data Selector**: `["results", "documents"]`

### Folders
- **Endpoint Path**: `/folders`
- **Data Selector**: `["results", "folders"]`

### Spaces
- **Endpoint Path**: `/spaces`
- **Data Selector**: `["results", "spaces"]`

### Webhooks
- **Endpoint Path**: `/folders/{{ stream_partition.folder_id }}/webhooks`
- **Data Selector**: `["results", "webhooks"]`

## Error Handling

- **Retry Logic**: Implement retry on common HTTP status codes such as 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 and a maximum of 3 retries.

## Schema

### Documents
- **Fields**:
  - `id`: string
  - `name`: string or null
  - `status`: string or null
  - `created_at_utc`: string or null
  - `updated_at_utc`: string or null
  - `completed_at_utc`: string or null
  - `sent_at_utc`: string or null
  - `signers`: array of objects or null
  - `variables`: array or null
  - `folder`: object or null
  - `space`: object or null

### Folders
- **Fields**:
  - `id`: number
  - `name`: string or null
  - `parent_id`: number or null
  - `space_id`: number or null

### Spaces
- **Fields**:
  - `id`: number
  - `name`: string or null
  - `allow_team_access`: boolean or null
  - `root_folder_id`: number or null

### Webhooks
- **Fields**:
  - `id`: number
  - `name`: string or null
  - `scope`: string or null
  - `target_url`: string or null
  - `triggers`: array of strings or null
  - `folder`: object or null

This configuration ensures a clean and efficient integration with the e-signature API, providing a robust solution for data synchronization and management.