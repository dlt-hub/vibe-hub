# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various resources, each with its own endpoint and configuration. The API uses bearer token authentication and supports error handling with retry logic.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["api_key"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "status_pages",
            "endpoint": {
                "path": "/v2/pages",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [401, 429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "page_components",
            "endpoint": {
                "path": "/v1/{page_id}/components",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [401, 429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "incidents",
            "endpoint": {
                "path": "/v1/{page_id}/incidents",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [401, 429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "incident_updates",
            "endpoint": {
                "path": "/v1/{parent_page_id}/incidents/{updates_object_id}/incident-updates/{incident_update_id}",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [401, 429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "maintenances",
            "endpoint": {
                "path": "/v1/{page_id}/maintenances",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [401, 429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "maintenance_updates",
            "endpoint": {
                "path": "/v1/{parent_page_id}/maintenances/{updates_object_id}/maintenance-updates/{maintenance_update_id}",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [401, 429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "templates",
            "endpoint": {
                "path": "/v1/{page_id}/templates",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [401, 429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "team",
            "endpoint": {
                "path": "/v1/{page_id}/team",
                "data_selector": "user",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [401, 429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "subscribers",
            "endpoint": {
                "path": "/v1/{page_id}/subscribers",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [401, 429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "metrics",
            "endpoint": {
                "path": "/v1/{page_id}/metrics",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [401, 429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "user",
            "endpoint": {
                "path": "/v1/user",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "public_data",
            "endpoint": {
                "path": "https://{url}.example.com/summary.json",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [401, 429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer
- **Token**: Retrieved from `dlt.secrets["api_key"]`

# Resources

- **Resource Name**: Abstracted from the original YAML
- **Endpoint Path**: Defined per resource, using placeholders for dynamic segments
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Single page (no pagination)
- **Data Selector**: Defined per resource

# Error Handling

- **Retry Logic**: 
  - Retry on status codes 401 and 429
  - Maximum retries: 3
  - Backoff factor: 2

# Schema

- **Fields**: Defined per resource schema
- **Types**: String, integer, boolean, array, object
- **Nullability**: Fields can be null
- **Neutral Field Names**: Used throughout the schema definitions