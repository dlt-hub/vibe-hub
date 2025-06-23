# Overview

This document provides a configuration guide for integrating with a generic third-party analytics API using the dltHub REST API source format. The API allows for the extraction of data related to various resources such as projects, runs, samples, and app sessions. The integration supports data synchronization through a RESTful interface, utilizing pagination and authentication mechanisms.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["access_token"]
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
                "path": "/users/current/projects",
                "data_selector": ["Response", "Items"],
                "paginator": {
                    "type": "offset",
                    "limit": 1024,
                    "offset_param": "Offset"
                }
            }
        },
        {
            "name": "runs",
            "endpoint": {
                "path": "/users/current/runs",
                "data_selector": ["Response", "Items"],
                "paginator": {
                    "type": "offset",
                    "limit": 1024,
                    "offset_param": "Offset"
                }
            }
        },
        {
            "name": "samples",
            "endpoint": {
                "path": "/projects/{{ stream_partition['parent_id'] }}/samples",
                "data_selector": ["Response", "Items"],
                "paginator": {
                    "type": "offset",
                    "limit": 1024,
                    "offset_param": "Offset"
                }
            }
        },
        {
            "name": "sample_files",
            "endpoint": {
                "path": "/samples/{{ stream_partition['parent_id'] }}/files",
                "data_selector": ["Response", "Items"],
                "paginator": {
                    "type": "offset",
                    "limit": 1024,
                    "offset_param": "Offset"
                }
            }
        },
        {
            "name": "run_files",
            "endpoint": {
                "path": "/runs/{{ stream_partition['parent_id'] }}/files",
                "data_selector": ["Response", "Items"],
                "paginator": {
                    "type": "offset",
                    "limit": 1024,
                    "offset_param": "Offset"
                }
            }
        },
        {
            "name": "appsessions",
            "endpoint": {
                "path": "/users/current/appsessions",
                "data_selector": ["Response", "Items"],
                "paginator": {
                    "type": "offset",
                    "limit": 1024,
                    "offset_param": "Offset"
                }
            }
        },
        {
            "name": "appresults",
            "endpoint": {
                "path": "/appsessions/{{ stream_partition['parent_id'] }}/appresults",
                "data_selector": ["Response", "Items"],
                "paginator": {
                    "type": "offset",
                    "limit": 1024,
                    "offset_param": "Offset"
                }
            }
        },
        {
            "name": "appresults_files",
            "endpoint": {
                "path": "/appresults/{{ stream_partition['parent_id'] }}/files",
                "data_selector": ["Response", "Items"]
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer
- **Token**: Access token is stored securely in `dlt.secrets["access_token"]`.

# Resources

- **Resource Name**: Abstracted from the original names (e.g., "projects", "runs").
- **Endpoint Path**: Defined for each resource, using placeholders for dynamic segments.
- **HTTP Method**: GET (default for all resources).
- **Pagination Strategy**: Offset-based pagination with parameters for limit and offset.
- **Data Selector**: Specifies the path to the data within the API response.

# Error Handling

- **Retry Logic**: Implement retry mechanisms for handling transient errors.
- **HTTP Status Codes**: Handle common HTTP errors such as 429 (Too Many Requests) and 5xx server errors.
- **Backoff Strategy**: Exponential backoff can be configured to manage retries.

# Schema

- **Fields**: Defined for each resource with types and nullability.
- **Neutral Field Names**: Use generic names like "id", "name", "date_created" to ensure vendor neutrality.
- **Additional Properties**: Allowed to accommodate any extra fields returned by the API.