# Overview

This document provides a configuration example for integrating with a third-party project management API using the dltHub REST API source. The integration allows for the extraction of various resources such as projects, stories, project memberships, labels, releases, epics, and activity logs. The API supports authentication via API key and utilizes offset-based pagination for certain resources.

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
            "name": "projects",
            "endpoint": {
                "path": "/v1/projects",
                "data_selector": "data"
            }
        },
        {
            "name": "stories",
            "endpoint": {
                "path": "/v1/projects/{project_id}/stories",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 10,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "project_memberships",
            "endpoint": {
                "path": "/v1/projects/{project_id}/memberships",
                "data_selector": "data"
            }
        },
        {
            "name": "labels",
            "endpoint": {
                "path": "/v1/projects/{project_id}/labels",
                "data_selector": "data"
            }
        },
        {
            "name": "releases",
            "endpoint": {
                "path": "/v1/projects/{project_id}/releases",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 10,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "epics",
            "endpoint": {
                "path": "/v1/projects/{project_id}/epics",
                "data_selector": "data"
            }
        },
        {
            "name": "activity",
            "endpoint": {
                "path": "/v1/projects/{project_id}/activity",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 10,
                    "offset_param": "offset"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: API Key
- **Secrets or Config Values**: Use `api_key` stored in `dlt.secrets["api_key"]`
- **Injection Location**: Header

# Resources

### Projects
- **Endpoint Path**: `/v1/projects`
- **Primary Key**: `id`
- **Data Selector**: `data`

### Stories
- **Endpoint Path**: `/v1/projects/{project_id}/stories`
- **Primary Key**: `id`
- **Data Selector**: `data`
- **Pagination Strategy**: Offset
  - **Limit**: 10
  - **Offset Parameter**: `offset`

### Project Memberships
- **Endpoint Path**: `/v1/projects/{project_id}/memberships`
- **Primary Key**: `id`
- **Data Selector**: `data`

### Labels
- **Endpoint Path**: `/v1/projects/{project_id}/labels`
- **Primary Key**: `id`
- **Data Selector**: `data`

### Releases
- **Endpoint Path**: `/v1/projects/{project_id}/releases`
- **Primary Key**: `id`
- **Data Selector**: `data`
- **Pagination Strategy**: Offset
  - **Limit**: 10
  - **Offset Parameter**: `offset`

### Epics
- **Endpoint Path**: `/v1/projects/{project_id}/epics`
- **Primary Key**: `id`
- **Data Selector**: `data`

### Activity
- **Endpoint Path**: `/v1/projects/{project_id}/activity`
- **Primary Key**: `guid`
- **Data Selector**: `data`
- **Pagination Strategy**: Offset
  - **Limit**: 10
  - **Offset Parameter**: `offset`

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

The schema for each resource is defined with neutral field names and types, ensuring compatibility with the dltHub format. Each resource's schema includes fields such as `id`, `kind`, `name`, `created_at`, `updated_at`, and other relevant attributes, with types like integer, string, boolean, and object.