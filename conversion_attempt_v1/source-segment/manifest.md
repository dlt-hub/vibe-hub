# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration allows for data extraction from various resources such as warehouses, sources, destinations, and more, using a RESTful approach. The API supports cursor-based pagination and requires authentication via an API token.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
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
            "name": "warehouses",
            "endpoint": {
                "path": "/warehouses",
                "data_selector": "data.warehouses",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "pagination.cursor",
                    "cursor_path": "data.pagination.next"
                }
            }
        },
        {
            "name": "sources",
            "endpoint": {
                "path": "/sources",
                "data_selector": "data.sources",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "pagination.cursor",
                    "cursor_path": "data.pagination.next"
                }
            }
        },
        {
            "name": "destinations",
            "endpoint": {
                "path": "/destinations",
                "data_selector": "data.destinations",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "pagination.cursor",
                    "cursor_path": "data.pagination.next"
                }
            }
        },
        {
            "name": "reverse_etl_models",
            "endpoint": {
                "path": "/reverse-etl-models",
                "data_selector": "data.models",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "pagination.cursor",
                    "cursor_path": "data.pagination.next"
                }
            }
        },
        {
            "name": "catalog_destinations",
            "endpoint": {
                "path": "/catalog/destinations",
                "data_selector": "data.destinationsCatalog",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "pagination.cursor",
                    "cursor_path": "data.pagination.next"
                }
            }
        },
        {
            "name": "catalog_sources",
            "endpoint": {
                "path": "/catalog/sources",
                "data_selector": "data.sourcesCatalog",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "pagination.cursor",
                    "cursor_path": "data.pagination.next"
                }
            }
        },
        {
            "name": "catalog_warehouses",
            "endpoint": {
                "path": "/catalog/warehouses",
                "data_selector": "data.warehousesCatalog",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "pagination.cursor",
                    "cursor_path": "data.pagination.next"
                }
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/users",
                "data_selector": "data.users",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "pagination.cursor",
                    "cursor_path": "data.pagination.next"
                }
            }
        },
        {
            "name": "labels",
            "endpoint": {
                "path": "/labels",
                "data_selector": "data.labels",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "pagination.cursor",
                    "cursor_path": "data.pagination.next"
                }
            }
        },
        {
            "name": "audit_events",
            "endpoint": {
                "path": "/roles",
                "data_selector": "data.roles",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "pagination.cursor",
                    "cursor_path": "data.pagination.next"
                }
            }
        },
        {
            "name": "transformations",
            "endpoint": {
                "path": "/transformations",
                "data_selector": "data.transformations",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "pagination.cursor",
                    "cursor_path": "data.pagination.next"
                }
            }
        },
        {
            "name": "spaces",
            "endpoint": {
                "path": "/spaces",
                "data_selector": "data.spaces",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "pagination.cursor",
                    "cursor_path": "data.pagination.next"
                }
            }
        },
        {
            "name": "usage_api_calls_daily",
            "endpoint": {
                "path": "/usage/api-calls/daily",
                "data_selector": "data.dailyWorkspaceAPICallsUsage",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "pagination.cursor",
                    "cursor_path": "data.pagination.next"
                }
            }
        },
        {
            "name": "usage_mtu_daily",
            "endpoint": {
                "path": "/usage/mtu/daily",
                "data_selector": "data.dailyWorkspaceMTUUsage",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "pagination.cursor",
                    "cursor_path": "data.pagination.next"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer
- **Token**: Retrieved from `dlt.secrets["api_token"]`

# Resources

- **Resource Names**: Warehouses, Sources, Destinations, Reverse ETL Models, Catalog Destinations, Catalog Sources, Catalog Warehouses, Users, Labels, Audit Events, Transformations, Spaces, Usage API Calls Daily, Usage MTU Daily
- **Endpoint Paths**: Each resource has a specific path, e.g., `/warehouses`, `/sources`, etc.
- **Pagination Strategy**: Cursor-based pagination using `pagination.cursor` as the cursor parameter and `data.pagination.next` as the cursor path.
- **Data Selector**: Each resource specifies a data selector to extract the relevant data from the API response.

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

- **Fields**: Each resource has a schema with fields such as `id`, `name`, `timestamp`, etc.
- **Types**: String, boolean, number, etc.
- **Nullability**: Fields can be nullable or non-nullable.
- **Primary Key**: Each resource has a primary key, e.g., `id` or `timestamp`.