# Overview

This document provides a vendor-neutral configuration for integrating with a third-party analytics API using the dltHub REST API source format. The integration supports various resources, each with its own endpoint and pagination strategy. The API requires authentication via an API key, and the configuration includes error handling and retry logic.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v1/",
        "auth": {
            "type": "api_key",
            "name": "X-API-Token",
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
            "name": "appointment_links",
            "endpoint": {
                "path": "/appointmentlinks/me",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "limit": 50,
                    "cursor_param": "next",
                    "stop_condition": "{{ response.get('hasNext', {}) == False }}"
                }
            }
        },
        {
            "name": "code_snippets",
            "endpoint": {
                "path": "/codesnippets",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 50,
                    "cursor_param": "next",
                    "stop_condition": "{{ response.get('hasNext', {}) == False }}"
                }
            }
        },
        {
            "name": "insights_reports",
            "endpoint": {
                "path": "/insightsreports",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 50,
                    "cursor_param": "next",
                    "stop_condition": "{{ response.get('hasNext', {}) == False }}"
                }
            }
        }
        // Additional resources can be added here following the same pattern
    ]
})
```

# Authentication

- **Type**: API Key
- **Header Name**: `X-API-Token`
- **Secret Key**: `api_key`

# Resources

Each resource is defined with a unique name, endpoint path, and pagination strategy. The data selector specifies the JSON path to extract data from the API response.

- **Resource Name**: Abstracted from the original stream names
- **Endpoint Path**: Generalized from the original paths
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Cursor-based with a limit of 50 items per page

# Error Handling

The configuration includes error handling with retry logic for specific HTTP status codes. The retry mechanism uses an exponential backoff strategy.

- **Retry on Status Codes**: [429]
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

The schema for each resource is defined with neutral field names and types. The primary key is specified for each resource to ensure data integrity.

- **Primary Key**: Abstracted from the original primary keys
- **Field Types**: String, number, boolean, array, object
- **Nullability**: Fields can be nullable

This configuration provides a clean and reusable pattern for integrating with a third-party analytics API using dltHub's REST API source format.