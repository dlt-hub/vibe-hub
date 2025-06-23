# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports various data resources, each with its own endpoint and configuration settings. The API allows for incremental synchronization based on datetime cursors and supports pagination for resource retrieval.

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
            "name": "current_user",
            "endpoint": {
                "path": "/current_user",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "service",
            "endpoint": {
                "path": "/service",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 20
                }
            }
        },
        {
            "name": "service_details",
            "endpoint": {
                "path": "/service/{service_id}/details",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 20
                }
            }
        },
        {
            "name": "service_version",
            "endpoint": {
                "path": "/service/{service_id}/version",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 20
                }
            }
        },
        {
            "name": "service_dictionaries",
            "endpoint": {
                "path": "/service/{service_id}/version/{version_id}/dictionary",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 20
                }
            }
        },
        {
            "name": "service_backend",
            "endpoint": {
                "path": "/service/{service_id}/version/{version_id}/backend",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 20
                }
            }
        },
        {
            "name": "service_domain",
            "endpoint": {
                "path": "/service/{service_id}/version/{version_id}/domain",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 20
                }
            }
        },
        {
            "name": "service_acl",
            "endpoint": {
                "path": "/service/{service_id}/version/{version_id}/acl",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 20
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access your API key.
- **Location**: The API key is included in the request header.

## Resources

### current_user
- **Endpoint Path**: `/current_user`
- **Pagination**: Single page
- **Data Selector**: `data`

### service
- **Endpoint Path**: `/service`
- **Pagination**: Page number
- **Data Selector**: `data`
- **Pagination Parameters**: `page`, `per_page`

### service_details
- **Endpoint Path**: `/service/{service_id}/details`
- **Pagination**: Page number
- **Data Selector**: `data`
- **Pagination Parameters**: `page`, `per_page`

### service_version
- **Endpoint Path**: `/service/{service_id}/version`
- **Pagination**: Page number
- **Data Selector**: `data`
- **Pagination Parameters**: `page`, `per_page`

### service_dictionaries
- **Endpoint Path**: `/service/{service_id}/version/{version_id}/dictionary`
- **Pagination**: Page number
- **Data Selector**: `data`
- **Pagination Parameters**: `page`, `per_page`

### service_backend
- **Endpoint Path**: `/service/{service_id}/version/{version_id}/backend`
- **Pagination**: Page number
- **Data Selector**: `data`
- **Pagination Parameters**: `page`, `per_page`

### service_domain
- **Endpoint Path**: `/service/{service_id}/version/{version_id}/domain`
- **Pagination**: Page number
- **Data Selector**: `data`
- **Pagination Parameters**: `page`, `per_page`

### service_acl
- **Endpoint Path**: `/service/{service_id}/version/{version_id}/acl`
- **Pagination**: Page number
- **Data Selector**: `data`
- **Pagination Parameters**: `page`, `per_page`

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

Each resource has its own schema, which includes fields such as `id`, `updated_at`, and other relevant data points. The schemas are designed to be flexible and accommodate various data types, including strings, numbers, and booleans.