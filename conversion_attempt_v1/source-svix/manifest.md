# Overview

This document provides a configuration guide for integrating with a third-party analytics API using the dltHub REST API source. The integration supports multiple resources, each with its own endpoint and data retrieval strategy. The API uses a bearer token for authentication and supports incremental synchronization based on datetime cursors.

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
            "name": "applications",
            "endpoint": {
                "path": "/v1/app",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "iterator",
                    "stop_condition": "done"
                }
            }
        },
        {
            "name": "event_types",
            "endpoint": {
                "path": "/v1/event-type",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "iterator",
                    "stop_condition": "done"
                }
            }
        },
        {
            "name": "tokens",
            "endpoint": {
                "path": "/v1/management/authentication/api-token",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "iterator",
                    "stop_condition": "done"
                }
            }
        },
        {
            "name": "endpoints",
            "endpoint": {
                "path": "/v1/app/{app_id}/endpoint",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "iterator",
                    "stop_condition": "done"
                }
            }
        },
        {
            "name": "integrations",
            "endpoint": {
                "path": "/v1/app/{app_id}/integration",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "iterator",
                    "stop_condition": "done"
                }
            }
        },
        {
            "name": "ingest_source",
            "endpoint": {
                "path": "/ingest/api/v1/source",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "iterator",
                    "stop_condition": "done"
                }
            }
        },
        {
            "name": "ingest_source_endpoint",
            "endpoint": {
                "path": "/ingest/api/v1/source/{src_id}/endpoint",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "iterator",
                    "stop_condition": "done"
                }
            }
        },
        {
            "name": "webhook_endpoint",
            "endpoint": {
                "path": "/v1/operational-webhook/endpoint",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "iterator",
                    "stop_condition": "done"
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

### Applications
- **Endpoint Path**: `/v1/app`
- **Primary Key**: `id`
- **Data Selector**: `data`
- **Pagination**: Cursor-based with `iterator` as the cursor parameter and `done` as the stop condition.

### Event Types
- **Endpoint Path**: `/v1/event-type`
- **Primary Key**: `uuid`
- **Data Selector**: `data`
- **Pagination**: Cursor-based with `iterator` as the cursor parameter and `done` as the stop condition.

### Tokens
- **Endpoint Path**: `/v1/management/authentication/api-token`
- **Primary Key**: `id`
- **Data Selector**: `data`
- **Pagination**: Cursor-based with `iterator` as the cursor parameter and `done` as the stop condition.

### Endpoints
- **Endpoint Path**: `/v1/app/{app_id}/endpoint`
- **Primary Key**: `id`
- **Data Selector**: `data`
- **Pagination**: Cursor-based with `iterator` as the cursor parameter and `done` as the stop condition.

### Integrations
- **Endpoint Path**: `/v1/app/{app_id}/integration`
- **Primary Key**: `id`
- **Data Selector**: `data`
- **Pagination**: Cursor-based with `iterator` as the cursor parameter and `done` as the stop condition.

### Ingest Source
- **Endpoint Path**: `/ingest/api/v1/source`
- **Primary Key**: `id`
- **Data Selector**: `data`
- **Pagination**: Cursor-based with `iterator` as the cursor parameter and `done` as the stop condition.

### Ingest Source Endpoint
- **Endpoint Path**: `/ingest/api/v1/source/{src_id}/endpoint`
- **Primary Key**: `id`
- **Data Selector**: `data`
- **Pagination**: Cursor-based with `iterator` as the cursor parameter and `done` as the stop condition.

### Webhook Endpoint
- **Endpoint Path**: `/v1/operational-webhook/endpoint`
- **Primary Key**: `id`
- **Data Selector**: `data`
- **Pagination**: Cursor-based with `iterator` as the cursor parameter and `done` as the stop condition.

## Error Handling

- **Retry Logic**: Implement retry on HTTP status codes 429, 500, 502, 503, 504 with a maximum of 3 retries and a backoff factor of 2.

## Schema

### Applications
- **Fields**: `metadata`, `createdAt`, `id`, `name`, `uid`, `updatedAt`
- **Required**: `id`, `updatedAt`

### Event Types
- **Fields**: `description`, `archived`, `createdAt`, `deprecated`, `featureFlag`, `name`, `updatedAt`, `uuid`
- **Required**: `uuid`, `updatedAt`

### Tokens
- **Fields**: `censoredToken`, `createdAt`, `id`, `name`
- **Required**: `id`, `createdAt`

### Endpoints
- **Fields**: `version`, `description`, `metadata`, `createdAt`, `disabled`, `id`, `updatedAt`, `url`
- **Required**: `id`, `updatedAt`

### Integrations
- **Fields**: `createdAt`, `featureFlags`, `id`, `name`, `updatedAt`
- **Required**: `id`, `updatedAt`

### Ingest Source
- **Fields**: `type`, `config`, `createdAt`, `id`, `name`, `updatedAt`
- **Required**: `id`, `updatedAt`

### Ingest Source Endpoint
- **Fields**: `description`, `metadata`, `createdAt`, `disabled`, `id`, `updatedAt`, `url`
- **Required**: `id`, `updatedAt`

### Webhook Endpoint
- **Fields**: `description`, `metadata`, `createdAt`, `disabled`, `filterTypes`, `id`, `rateLimit`, `uid`, `updatedAt`, `url`
- **Required**: `id`, `updatedAt`