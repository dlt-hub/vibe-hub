# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration allows for the synchronization of data related to various resources such as jobs, connections, and workspaces. The configuration is designed to be vendor-neutral and does not include any proprietary or branded content.

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
            "name": "jobs",
            "endpoint": {
                "path": "/v1/jobs",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "connections",
            "endpoint": {
                "path": "/v1/connections",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next"
                }
            }
        },
        {
            "name": "workspaces",
            "endpoint": {
                "path": "/v1/workspaces",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "next"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: OAuth2
- **Secrets**: 
  - `client_id`: Your client ID for authentication.
  - `client_secret`: Your client secret for authentication.
- **Token URL**: `https://api.example.com/token`

## Resources

### Jobs

- **Endpoint Path**: `/v1/jobs`
- **Primary Key**: `jobId`
- **Pagination Strategy**: Offset
  - **Limit**: 100
  - **Offset Parameter**: `offset`
- **Data Selector**: `data`

### Connections

- **Endpoint Path**: `/v1/connections`
- **Primary Key**: `connectionId`
- **Pagination Strategy**: Cursor
  - **Cursor Parameter**: `next`
- **Data Selector**: `data`

### Workspaces

- **Endpoint Path**: `/v1/workspaces`
- **Primary Key**: `workspaceId`
- **Pagination Strategy**: Cursor
  - **Cursor Parameter**: `next`
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: 
  - **Retry on Status Codes**: [429, 500, 502, 503, 504]
  - **Max Retries**: 3
  - **Backoff Factor**: 2

## Schema

### Jobs

- **Fields**:
  - `jobId`: number
  - `lastUpdatedAt`: string
  - `bytesSynced`: number or null
  - `connectionId`: string or null
  - `duration`: string or null
  - `jobType`: string or null
  - `rowsSynced`: number or null
  - `startTime`: string or null
  - `status`: string or null

### Connections

- **Fields**:
  - `connectionId`: string
  - `createdAt`: number or null
  - `dataResidency`: string or null
  - `destinationId`: string or null
  - `name`: string or null
  - `namespaceDefinition`: string or null
  - `namespaceFormat`: string or null
  - `nonBreakingSchemaUpdatesBehavior`: string or null
  - `prefix`: string or null
  - `schedule`: object or null
  - `sourceId`: string or null
  - `status`: string or null
  - `tags`: array or null
  - `workspaceId`: string or null

### Workspaces

- **Fields**:
  - `workspaceId`: string
  - `dataResidency`: string or null
  - `name`: string or null
  - `notifications`: object or null

This configuration guide provides a comprehensive overview of how to set up and use the dltHub REST API source for syncing data from a third-party analytics API. The guide ensures a clean and vendor-neutral approach, focusing solely on the necessary parameters and patterns.