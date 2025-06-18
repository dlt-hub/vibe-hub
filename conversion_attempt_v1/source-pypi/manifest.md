# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source format. The integration supports retrieving data about projects, releases, and statistics from the API. The configuration is designed to be vendor-neutral and follows the dltHub structure for REST API sources.

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
            "location": "query"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "project",
            "endpoint": {
                "path": "/v1/project",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "release",
            "endpoint": {
                "path": "/v1/release",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "stats",
            "endpoint": {
                "path": "/v1/stats",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: Use `api_key` stored in `dlt.secrets`.

## Resources

### Project Resource

- **Resource Name**: `project`
- **Endpoint Path**: `/v1/project`
- **HTTP Method**: `GET`
- **Pagination Strategy**: `single_page`
- **Data Selector**: `data`

### Release Resource

- **Resource Name**: `release`
- **Endpoint Path**: `/v1/release`
- **HTTP Method**: `GET`
- **Pagination Strategy**: `single_page`
- **Data Selector**: `data`

### Stats Resource

- **Resource Name**: `stats`
- **Endpoint Path**: `/v1/stats`
- **HTTP Method**: `GET`
- **Pagination Strategy**: `single_page`
- **Data Selector**: `data`

## Error Handling

- **Retry/Backoff Logic**: Not specified in the YAML, default dltHub behavior applies.
- **Known HTTP Response Codes**: Not specified in the YAML, default dltHub behavior applies.
- **Fallback/Handling Behavior**: Not specified in the YAML, default dltHub behavior applies.

## Schema

The schema for each resource is defined in a neutral format, with fields such as `version`, `author`, `author_email`, `license`, `name`, `project_url`, and others. These fields are used to describe the metadata and details of the projects and releases. The schema is consistent with the YAML input and uses neutral field names.