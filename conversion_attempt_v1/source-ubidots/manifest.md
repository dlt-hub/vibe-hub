# Overview

This document provides a configuration guide for integrating with a generic IoT platform API. The integration allows users to fetch, sync, and analyze real-time sensor data, streamlining IoT workflows by connecting the platform with other tools for seamless data processing and insights.

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
            "api_key": dlt.secrets["api_token"],
            "location": "header"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "devices",
            "endpoint": {
                "path": "/v1/devices",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 200,
                    "cursor_param": "page",
                    "stop_condition": "response.next is none"
                }
            }
        },
        {
            "name": "events",
            "endpoint": {
                "path": "/v1/events",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 200,
                    "cursor_param": "page",
                    "stop_condition": "response.next is none"
                }
            }
        },
        {
            "name": "dashboards",
            "endpoint": {
                "path": "/v1/dashboards",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 200,
                    "cursor_param": "page",
                    "stop_condition": "response.next is none"
                }
            }
        },
        {
            "name": "variables",
            "endpoint": {
                "path": "/v1/variables",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "page",
                    "stop_condition": "response.next is none"
                }
            }
        },
        {
            "name": "device_groups",
            "endpoint": {
                "path": "/v1/device_groups",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "page",
                    "stop_condition": "response.next is none"
                }
            }
        },
        {
            "name": "device_types",
            "endpoint": {
                "path": "/v1/device_types",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "page",
                    "stop_condition": "response.next is none"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: Use `api_token` stored in `dlt.secrets` for authentication.
- **Location**: Header

## Resources

### Devices
- **Endpoint Path**: `/v1/devices`
- **Data Selector**: `results`
- **Pagination Strategy**: Cursor-based with `page` as the cursor parameter and a limit of 200.

### Events
- **Endpoint Path**: `/v1/events`
- **Data Selector**: `results`
- **Pagination Strategy**: Cursor-based with `page` as the cursor parameter and a limit of 200.

### Dashboards
- **Endpoint Path**: `/v1/dashboards`
- **Data Selector**: `results`
- **Pagination Strategy**: Cursor-based with `page` as the cursor parameter and a limit of 200.

### Variables
- **Endpoint Path**: `/v1/variables`
- **Data Selector**: `results`
- **Pagination Strategy**: Cursor-based with `page` as the cursor parameter.

### Device Groups
- **Endpoint Path**: `/v1/device_groups`
- **Data Selector**: `results`
- **Pagination Strategy**: Cursor-based with `page` as the cursor parameter.

### Device Types
- **Endpoint Path**: `/v1/device_types`
- **Data Selector**: `results`
- **Pagination Strategy**: Cursor-based with `page` as the cursor parameter.

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

### Devices
- **Fields**: `id`, `description`, `createdAt`, `isActive`, `label`, `lastActivity`, `name`, `properties`, `tags`, `url`, `variables`, `variablesCount`
- **Primary Key**: `id`

### Events
- **Fields**: `id`, `description`, `actions`, `activeDates`, `createdAt`, `isActive`, `isGlobalEvent`, `label`, `name`, `tags`, `triggers`, `url`
- **Primary Key**: `id`

### Dashboards
- **Fields**: `id`, `description`, `context`, `createdAt`, `isActive`, `isEditable`, `label`, `name`, `order`, `tags`, `timeframe`, `url`, `widgets`, `widgetsNumber`
- **Primary Key**: `id`

### Variables
- **Fields**: `id`, `type`, `description`, `createdAt`, `device`, `label`, `lastActivity`, `lastValue`, `name`, `properties`, `syntheticExpression`, `tags`, `url`, `valuesUrl`
- **Primary Key**: `id`

### Device Groups
- **Fields**: `id`, `createdAt`, `devices`, `devicesCount`, `label`, `name`, `tags`, `url`
- **Primary Key**: `id`

### Device Types
- **Fields**: `id`, `description`, `createdAt`, `deviceColor`, `deviceIcon`, `label`, `name`, `properties`, `tags`, `tasks`, `url`, `variableColor`, `variables`
- **Primary Key**: `id`