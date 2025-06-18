# Overview

This document provides a configuration guide for integrating with a third-party analytics API using the dltHub REST API source. The integration allows for the extraction and synchronization of various resources such as items, occurrences, metrics, deploys, environments, jobs, projects, teams, and users. The API supports both full and incremental synchronization based on datetime cursors.

# Configuration Example

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
            "name": "items",
            "endpoint": {
                "path": "/v1/items",
                "data_selector": "result.items",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 100
                }
            }
        },
        {
            "name": "occurrences",
            "endpoint": {
                "path": "/v1/occurrences",
                "data_selector": "result.instances",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 20
                }
            }
        },
        {
            "name": "items_metrics",
            "endpoint": {
                "path": "/v1/metrics/items",
                "data_selector": "result.metrics",
                "method": "POST"
            }
        },
        {
            "name": "deploys",
            "endpoint": {
                "path": "/v1/deploys",
                "data_selector": "result.deploys",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 20
                }
            }
        },
        {
            "name": "environments",
            "endpoint": {
                "path": "/v1/environments",
                "data_selector": "result.environments",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 20
                }
            }
        },
        {
            "name": "rql_jobs",
            "endpoint": {
                "path": "/v1/rql/jobs",
                "data_selector": "result.jobs",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 20
                }
            }
        },
        {
            "name": "top_active_items",
            "endpoint": {
                "path": "/v1/reports/top_active_items",
                "data_selector": "result.*.item"
            }
        },
        {
            "name": "projects",
            "endpoint": {
                "path": "/v1/projects",
                "data_selector": "result"
            }
        },
        {
            "name": "teams",
            "endpoint": {
                "path": "/v1/teams",
                "data_selector": "result"
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/v1/users",
                "data_selector": "result.users"
            }
        }
    ]
})
```

# Authentication

- **Type**: `api_key`
- **Secrets**: Use `dlt.secrets["api_key"]` for the API key.
- **Location**: The API key is included in the request header.

# Resources

## Items
- **Endpoint Path**: `/v1/items`
- **Pagination**: Page number with `page` parameter, limit of 100.
- **Data Selector**: `result.items`

## Occurrences
- **Endpoint Path**: `/v1/occurrences`
- **Pagination**: Page number with `page` parameter, limit of 20.
- **Data Selector**: `result.instances`

## Items Metrics
- **Endpoint Path**: `/v1/metrics/items`
- **Method**: POST
- **Data Selector**: `result.metrics`

## Deploys
- **Endpoint Path**: `/v1/deploys`
- **Pagination**: Page number with `page` parameter, limit of 20.
- **Data Selector**: `result.deploys`

## Environments
- **Endpoint Path**: `/v1/environments`
- **Pagination**: Page number with `page` parameter, limit of 20.
- **Data Selector**: `result.environments`

## RQL Jobs
- **Endpoint Path**: `/v1/rql/jobs`
- **Pagination**: Page number with `page` parameter, limit of 20.
- **Data Selector**: `result.jobs`

## Top Active Items
- **Endpoint Path**: `/v1/reports/top_active_items`
- **Data Selector**: `result.*.item`

## Projects
- **Endpoint Path**: `/v1/projects`
- **Data Selector**: `result`

## Teams
- **Endpoint Path**: `/v1/teams`
- **Data Selector**: `result`

## Users
- **Endpoint Path**: `/v1/users`
- **Data Selector**: `result.users`

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

Each resource has a defined schema with fields, types, and nullability. Use neutral field names such as `analytics_id` instead of any branded identifiers.