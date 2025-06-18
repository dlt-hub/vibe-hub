# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports data synchronization for various resources, including components, issues, and metrics. The API uses bearer token authentication and supports pagination for data retrieval.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["user_token"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "components",
            "endpoint": {
                "path": "/v1/components/search",
                "data_selector": "components",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "p",
                    "limit_param": "ps"
                }
            }
        },
        {
            "name": "issues",
            "endpoint": {
                "path": "/v1/issues/search",
                "data_selector": "issues",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "p",
                    "limit_param": "ps"
                }
            }
        },
        {
            "name": "metrics",
            "endpoint": {
                "path": "/v1/metrics/search",
                "data_selector": "metrics",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "p",
                    "limit_param": "ps"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Bearer Token
- **Token**: Stored securely in `dlt.secrets["user_token"]`

## Resources

### Components

- **Endpoint Path**: `/v1/components/search`
- **Data Selector**: `components`
- **Pagination Strategy**: Page number with parameters `p` for page and `ps` for page size

### Issues

- **Endpoint Path**: `/v1/issues/search`
- **Data Selector**: `issues`
- **Pagination Strategy**: Page number with parameters `p` for page and `ps` for page size

### Metrics

- **Endpoint Path**: `/v1/metrics/search`
- **Data Selector**: `metrics`
- **Pagination Strategy**: Page number with parameters `p` for page and `ps` for page size

## Error Handling

- **Retry Logic**: Implemented with exponential backoff
- **Retry on Status Codes**: 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

### Components

- **Fields**:
  - `key`: Unique identifier for the component
  - `name`: Name of the component
  - `organization`: Name of the organization
  - `project`: Name of the associated project
  - `qualifier`: Type of the component

### Issues

- **Fields**:
  - `type`: Category of the issue
  - `author`: Creator of the issue
  - `component`: Associated project component
  - `creationDate`: Date and time of creation
  - `debt`: Debt value for resolution
  - `effort`: Estimated effort for resolution
  - `flows`: Code flow information
  - `hash`: Unique hash identifier
  - `key`: Unique key or identifier
  - `line`: Line number in source code
  - `message`: Description of the issue
  - `organization`: Organization name
  - `project`: Project name
  - `resolution`: Resolution status
  - `rule`: Rule identifier
  - `severity`: Severity level
  - `status`: Current status
  - `tags`: Associated tags or labels
  - `textRange`: Text range in source code
  - `updateDate`: Last update date and time

### Metrics

- **Fields**:
  - `type`: Format of the metric data
  - `description`: Summary of the metric data
  - `direction`: Direction of the metric data
  - `domain`: Domain or category
  - `hidden`: Visibility status
  - `id`: Unique identifier
  - `key`: Associated key
  - `name`: Name or title
  - `qualitative`: Qualitative nature indicator

This configuration provides a comprehensive setup for integrating with the API, ensuring secure authentication, efficient data retrieval, and robust error handling.