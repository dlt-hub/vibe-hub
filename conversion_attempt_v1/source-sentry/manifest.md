# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports incremental data synchronization and pagination, allowing for efficient data retrieval from the API.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["auth_token"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "events",
            "endpoint": {
                "path": "/v1/projects/{organization}/{project}/events/",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "cursor",
                    "cursor_path": "headers.link.next.cursor",
                    "stop_condition": "headers.link.next.results == 'false'"
                }
            }
        },
        {
            "name": "issues",
            "endpoint": {
                "path": "/v1/projects/{organization}/{project}/issues/",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "cursor",
                    "cursor_path": "headers.link.next.cursor",
                    "stop_condition": "headers.link.next.results == 'false'"
                }
            }
        },
        {
            "name": "projects",
            "endpoint": {
                "path": "/v1/projects/",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "cursor",
                    "cursor_path": "headers.link.next.cursor",
                    "stop_condition": "headers.link.next.results == 'false'"
                }
            }
        },
        {
            "name": "releases",
            "endpoint": {
                "path": "/v1/organizations/{organization}/releases/",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "cursor",
                    "cursor_path": "headers.link.next.cursor",
                    "stop_condition": "headers.link.next.results == 'false'"
                }
            }
        },
        {
            "name": "project_detail",
            "endpoint": {
                "path": "/v1/projects/{organization}/{project}/",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "cursor",
                    "cursor_path": "headers.link.next.cursor",
                    "stop_condition": "headers.link.next.results == 'false'"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer
- **Token**: Retrieved from `dlt.secrets["auth_token"]`

# Resources

## Events
- **Endpoint Path**: `/v1/projects/{organization}/{project}/events/`
- **Pagination Strategy**: Cursor-based
- **Data Selector**: `data`

## Issues
- **Endpoint Path**: `/v1/projects/{organization}/{project}/issues/`
- **Pagination Strategy**: Cursor-based
- **Data Selector**: `data`

## Projects
- **Endpoint Path**: `/v1/projects/`
- **Pagination Strategy**: Cursor-based
- **Data Selector**: `data`

## Releases
- **Endpoint Path**: `/v1/organizations/{organization}/releases/`
- **Pagination Strategy**: Cursor-based
- **Data Selector**: `data`

## Project Detail
- **Endpoint Path**: `/v1/projects/{organization}/{project}/`
- **Pagination Strategy**: Cursor-based
- **Data Selector**: `data`

# Error Handling

- **Retry Logic**: Implemented with exponential backoff
- **HTTP Status Codes**: Retries on 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

The schema for each resource is defined with neutral field names and types, ensuring compatibility with the dltHub format. Each resource includes a primary key and supports incremental synchronization based on a datetime cursor field.