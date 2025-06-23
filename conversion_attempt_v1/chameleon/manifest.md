# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various resources, each with its own endpoint and data extraction logic. The API allows for incremental synchronization based on datetime fields and supports error handling with retry logic.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v3/",
        "auth": {
            "type": "api_key",
            "name": "api_key",
            "api_key": dlt.secrets["api_key"],
            "location": "header"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge",
        "endpoint": {
            "params": {
                "limit": 50
            }
        }
    },
    "resources": [
        {
            "name": "helpbars",
            "endpoint": {
                "path": "/edit/searches",
                "data_selector": "searches",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "segments",
            "endpoint": {
                "path": "/edit/segments",
                "data_selector": "segments",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "urls",
            "endpoint": {
                "path": "/edit/urls",
                "data_selector": "urls",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "url_groups",
            "endpoint": {
                "path": "/edit/url_groups",
                "data_selector": "url_groups",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "changes",
            "endpoint": {
                "path": "/edit/changes",
                "data_selector": "changes",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "launchers",
            "endpoint": {
                "path": "/edit/launchers",
                "data_selector": "launchers",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "tooltips",
            "endpoint": {
                "path": "/edit/tooltips",
                "data_selector": "tooltips",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "tours",
            "endpoint": {
                "path": "/edit/tours",
                "data_selector": "tours",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "surveys",
            "endpoint": {
                "path": "/edit/surveys",
                "data_selector": "surveys",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "survey_responses",
            "endpoint": {
                "path": "/analyze/responses",
                "data_selector": "responses",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: `api_key`
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access the API key.
- **Location**: The API key is included in the request headers.

# Resources

Each resource is defined with a unique name, endpoint path, and data extraction logic. The resources support incremental synchronization using datetime fields and are configured with error handling and retry logic.

- **Resource Name**: Abstracted from the original YAML
- **Endpoint Path**: Generalized to `/v1/resource`
- **HTTP Method**: GET (default)
- **Data Selector**: Extracts data from the specified field path
- **Pagination Strategy**: Offset-based pagination with a configurable limit
- **Primary Key**: `id`
- **Write Disposition**: `merge`

# Error Handling

The configuration includes error handling with retry logic for specific HTTP status codes. The retry mechanism uses an exponential backoff strategy.

- **Retry on Status Codes**: [429]
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

The schema for each resource is defined with neutral field names and types. The schema is consistent with the original YAML and supports additional properties.

- **Fields**: Defined with types and nullability
- **Required Fields**: `id`, `updated_at`