# Overview

This document provides a configuration example for integrating with a third-party incident management and reliability API using the dltHub REST API source. This integration allows for efficient extraction of incident management data, enabling teams to gain insights for post-incident analysis, reliability reporting, and proactive system monitoring. The integration automates data flow and enhances visibility into incidents and response metrics, improving operational efficiency.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v1",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["api_token"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "environments",
            "endpoint": {
                "path": "/environments",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 200,
                    "cursor_param": "page",
                    "cursor_path": "pagination.next"
                }
            }
        },
        {
            "name": "services",
            "endpoint": {
                "path": "/services",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 200,
                    "cursor_param": "page",
                    "cursor_path": "pagination.next"
                }
            }
        },
        {
            "name": "functionalities",
            "endpoint": {
                "path": "/functionalities",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 200,
                    "cursor_param": "page",
                    "cursor_path": "pagination.next"
                }
            }
        },
        {
            "name": "teams",
            "endpoint": {
                "path": "/teams",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 200,
                    "cursor_param": "page",
                    "cursor_path": "pagination.next"
                }
            }
        },
        {
            "name": "webhooks",
            "endpoint": {
                "path": "/webhooks",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 200,
                    "cursor_param": "page",
                    "cursor_path": "pagination.next"
                }
            }
        }
        // Additional resources can be added here following the same pattern
    ]
})
```

# Authentication

- **Type**: Bearer
- **Token**: Use the `api_token` stored in dlt secrets for authentication.

# Resources

Each resource is configured with the following parameters:

- **Resource Name**: A neutral name representing the data being accessed.
- **Endpoint Path**: The API path to access the resource.
- **HTTP Method**: GET (default)
- **Data Selector**: The JSON path to extract data from the API response.
- **Pagination Strategy**: Cursor-based pagination with a limit of 200 records per page.

# Error Handling

The configuration includes retry logic for handling transient errors:

- **Retry on Status Codes**: [429, 500, 502, 503, 504]
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

The schema for each resource is defined with neutral field names and types, ensuring compatibility with various data ecosystems. Each resource's schema is consistent with the fields and types specified in the YAML configuration.