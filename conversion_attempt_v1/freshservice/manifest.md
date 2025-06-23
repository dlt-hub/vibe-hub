# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source format. The integration supports various resources such as tickets, satisfaction survey responses, requested items, problems, changes, releases, requesters, agents, locations, products, vendors, assets, software, and purchase orders. Each resource is configured to retrieve data from a specified endpoint, with support for pagination and error handling.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/api/v2",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["api_key"],
            "password": dlt.secrets["api_key"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "tickets",
            "endpoint": {
                "path": "/tickets",
                "data_selector": "tickets",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 30
                },
                "error_handler": {
                    "retry_on_status_codes": [403],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "satisfaction_survey_responses",
            "endpoint": {
                "path": "/tickets/{parent_id}/csat_response",
                "data_selector": "csat_response",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 30
                },
                "error_handler": {
                    "retry_on_status_codes": [403],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "requested_items",
            "endpoint": {
                "path": "/tickets/{parent_id}/requested_items",
                "data_selector": "requested_items",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 30
                },
                "error_handler": {
                    "retry_on_status_codes": [403, 404],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "problems",
            "endpoint": {
                "path": "/problems",
                "data_selector": "problems",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 30
                },
                "error_handler": {
                    "retry_on_status_codes": [403],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "changes",
            "endpoint": {
                "path": "/changes",
                "data_selector": "changes",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 30
                },
                "error_handler": {
                    "retry_on_status_codes": [403],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "releases",
            "endpoint": {
                "path": "/releases",
                "data_selector": "releases",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 30
                },
                "error_handler": {
                    "retry_on_status_codes": [403],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "requesters",
            "endpoint": {
                "path": "/requesters",
                "data_selector": "requesters",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 30
                },
                "error_handler": {
                    "retry_on_status_codes": [403],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "agents",
            "endpoint": {
                "path": "/agents",
                "data_selector": "agents",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 30
                },
                "error_handler": {
                    "retry_on_status_codes": [403],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "locations",
            "endpoint": {
                "path": "/locations",
                "data_selector": "locations",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 30
                },
                "error_handler": {
                    "retry_on_status_codes": [403],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "products",
            "endpoint": {
                "path": "/products",
                "data_selector": "products",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 30
                },
                "error_handler": {
                    "retry_on_status_codes": [403],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "vendors",
            "endpoint": {
                "path": "/vendors",
                "data_selector": "vendors",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 30
                },
                "error_handler": {
                    "retry_on_status_codes": [403],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "assets",
            "endpoint": {
                "path": "/assets",
                "data_selector": "assets",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 30
                },
                "error_handler": {
                    "retry_on_status_codes": [403],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "software",
            "endpoint": {
                "path": "/applications",
                "data_selector": "applications",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 30
                },
                "error_handler": {
                    "retry_on_status_codes": [403],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "purchase_orders",
            "endpoint": {
                "path": "/purchase_orders",
                "data_selector": "purchase_orders",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 30
                },
                "error_handler": {
                    "retry_on_status_codes": [403],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Basic Authentication
- **Secrets**: 
  - `api_key`: The API key used for authentication.

# Resources

Each resource is configured with the following parameters:

- **Resource Name**: A generalized name for the resource.
- **Endpoint Path**: The path to the resource endpoint.
- **HTTP Method**: GET (default).
- **Data Selector**: The field path to extract data from the response.
- **Primary Key**: The unique identifier for each record.
- **Write Disposition**: Merge (default).
- **Pagination Strategy**: Page number-based pagination with a limit of 30 records per page.
- **Error Handling**: Retry logic for specific HTTP status codes with a maximum of 3 retries and a backoff factor of 2.

# Error Handling

- **Retry Logic**: Configured to retry on HTTP status codes 403 and 404 (where applicable).
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

The schema for each resource is defined with fields, types, and descriptions. Field names are generalized to ensure vendor neutrality. For example, `id` is used as the primary key for all resources.