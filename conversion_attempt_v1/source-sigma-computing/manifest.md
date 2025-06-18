# Overview

This document provides a configuration example for integrating with a third-party analytics API using dltHub's REST API source. The integration allows for the extraction of data from various resources such as files, connections, datasets, members, teams, templates, workspaces, and workbooks. The API supports OAuth2 authentication and paginated data retrieval.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v2",
        "auth": {
            "type": "oauth2",
            "client_id": dlt.secrets["client_id"],
            "client_secret": dlt.secrets["client_secret"],
            "refresh_token": dlt.secrets["client_refresh_token"],
            "token_url": "https://api.example.com/v2/auth/token"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge",
        "endpoint": {
            "params": {
                "limit": 100
            }
        }
    },
    "resources": [
        {
            "name": "files",
            "endpoint": {
                "path": "/files",
                "data_selector": "entries",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "connections",
            "endpoint": {
                "path": "/connections",
                "data_selector": "entries",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "datasets",
            "endpoint": {
                "path": "/datasets",
                "data_selector": "entries",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "members",
            "endpoint": {
                "path": "/members",
                "data_selector": "entries",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "teams",
            "endpoint": {
                "path": "/teams",
                "data_selector": "entries",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "templates",
            "endpoint": {
                "path": "/templates",
                "data_selector": "entries",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "workspaces",
            "endpoint": {
                "path": "/workspaces",
                "data_selector": "entries",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "workbooks",
            "endpoint": {
                "path": "/workbooks",
                "data_selector": "entries",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "start_from_page": 1
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: OAuth2
- **Secrets**:
  - `client_id`
  - `client_secret`
  - `client_refresh_token`
- **Token URL**: `https://api.example.com/v2/auth/token`

## Resources

### Files
- **Endpoint Path**: `/files`
- **Pagination**: Page number
- **Data Selector**: `entries`

### Connections
- **Endpoint Path**: `/connections`
- **Pagination**: Page number
- **Data Selector**: `entries`

### Datasets
- **Endpoint Path**: `/datasets`
- **Pagination**: Page number
- **Data Selector**: `entries`

### Members
- **Endpoint Path**: `/members`
- **Pagination**: Page number
- **Data Selector**: `entries`

### Teams
- **Endpoint Path**: `/teams`
- **Pagination**: Page number
- **Data Selector**: `entries`

### Templates
- **Endpoint Path**: `/templates`
- **Pagination**: Page number
- **Data Selector**: `entries`

### Workspaces
- **Endpoint Path**: `/workspaces`
- **Pagination**: Page number
- **Data Selector**: `entries`

### Workbooks
- **Endpoint Path**: `/workbooks`
- **Pagination**: Page number
- **Data Selector**: `entries`

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

Each resource has a schema defined with fields such as `id`, `createdAt`, `updatedAt`, and other relevant attributes. The schemas are designed to be flexible with additional properties allowed. Field names are generalized to ensure neutrality.