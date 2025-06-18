# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source. The integration supports various data resources, each with its own endpoint and configuration settings. The API allows for incremental data synchronization and supports pagination and error handling mechanisms.

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
            "location": "header"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "profiles",
            "endpoint": {
                "path": "/v1/profiles",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_value": "{{ response.get('links', {}).get('next') }}"
                }
            }
        },
        {
            "name": "global_exclusions",
            "endpoint": {
                "path": "/v1/profiles",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_value": "{{ response.get('links', {}).get('next') }}"
                }
            }
        },
        {
            "name": "events",
            "endpoint": {
                "path": "/v1/events",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_value": "{{ response.get('links', {}).get('next') }}"
                }
            }
        },
        {
            "name": "email_templates",
            "endpoint": {
                "path": "/v1/templates",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_value": "{{ response.get('links', {}).get('next') }}"
                }
            }
        },
        {
            "name": "metrics",
            "endpoint": {
                "path": "/v1/metrics",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_value": "{{ response.get('links', {}).get('next') }}"
                }
            }
        },
        {
            "name": "lists",
            "endpoint": {
                "path": "/v1/lists",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_value": "{{ response.get('links', {}).get('next') }}"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: Use `api_key` stored in `dlt.secrets["api_key"]`
- **Location**: Header

## Resources

### Profiles
- **Endpoint Path**: `/v1/profiles`
- **Pagination Strategy**: Cursor-based
- **Data Selector**: `data`

### Global Exclusions
- **Endpoint Path**: `/v1/profiles`
- **Pagination Strategy**: Cursor-based
- **Data Selector**: `data`

### Events
- **Endpoint Path**: `/v1/events`
- **Pagination Strategy**: Cursor-based
- **Data Selector**: `data`

### Email Templates
- **Endpoint Path**: `/v1/templates`
- **Pagination Strategy**: Cursor-based
- **Data Selector**: `data`

### Metrics
- **Endpoint Path**: `/v1/metrics`
- **Pagination Strategy**: Cursor-based
- **Data Selector**: `data`

### Lists
- **Endpoint Path**: `/v1/lists`
- **Pagination Strategy**: Cursor-based
- **Data Selector**: `data`

## Error Handling

- **Retry/Backoff Logic**: 
  - Retry on status codes: 429, 500, 502, 503, 504
  - Max retries: 3
  - Backoff factor: 2
- **Known HTTP Response Codes**: 
  - 401, 403: Configuration error, ensure valid API key and permissions
  - 429: Rate limited, retry after specified time

## Schema

- **Fields**: Use neutral field names such as `customer_id`, `event_timestamp`, etc.
- **Types**: String, integer, boolean, date-time
- **Nullability**: Fields can be nullable

This configuration guide provides a clean, vendor-neutral setup for integrating with a third-party analytics API using dltHub's REST API source. It includes authentication, resource configuration, pagination, error handling, and schema details.