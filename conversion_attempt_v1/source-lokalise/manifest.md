# Overview

This document provides a configuration example for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports multiple resources, each with its own endpoint and pagination strategy. The API requires authentication via an API key, which is included in the request headers.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "api_key",
            "name": "X-Api-Token",
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
            "name": "keys",
            "endpoint": {
                "path": "/v1/projects/{project_id}/keys",
                "data_selector": "keys",
                "paginator": {
                    "type": "page_number",
                    "limit": 1000,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "languages",
            "endpoint": {
                "path": "/v1/projects/{project_id}/languages",
                "data_selector": "languages",
                "paginator": {
                    "type": "page_number",
                    "limit": 1000,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "comments",
            "endpoint": {
                "path": "/v1/projects/{project_id}/comments",
                "data_selector": "comments",
                "paginator": {
                    "type": "page_number",
                    "limit": 1000,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "contributors",
            "endpoint": {
                "path": "/v1/projects/{project_id}/contributors",
                "data_selector": "contributors",
                "paginator": {
                    "type": "page_number",
                    "limit": 1000,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "translations",
            "endpoint": {
                "path": "/v1/projects/{project_id}/translations",
                "data_selector": "translations",
                "paginator": {
                    "type": "page_number",
                    "limit": 1000,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: API Key
- **Header Name**: `X-Api-Token`
- **API Key**: Stored securely in `dlt.secrets["api_key"]`

## Resources

### Keys
- **Endpoint Path**: `/v1/projects/{project_id}/keys`
- **Data Selector**: `keys`
- **Pagination**: Page number with `page` and `limit` parameters

### Languages
- **Endpoint Path**: `/v1/projects/{project_id}/languages`
- **Data Selector**: `languages`
- **Pagination**: Page number with `page` and `limit` parameters

### Comments
- **Endpoint Path**: `/v1/projects/{project_id}/comments`
- **Data Selector**: `comments`
- **Pagination**: Page number with `page` and `limit` parameters

### Contributors
- **Endpoint Path**: `/v1/projects/{project_id}/contributors`
- **Data Selector**: `contributors`
- **Pagination**: Page number with `page` and `limit` parameters

### Translations
- **Endpoint Path**: `/v1/projects/{project_id}/translations`
- **Data Selector**: `translations`
- **Pagination**: Page number with `page` and `limit` parameters

## Error Handling

- **Retry Logic**: Implement retry on specific HTTP status codes (e.g., 429, 500, 502, 503, 504)
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

The schema for each resource is defined with neutral field names and types, ensuring compatibility with the API's data structure. Each resource's schema includes fields such as `id`, `name`, `created_at`, and other relevant attributes, with appropriate data types and nullability.