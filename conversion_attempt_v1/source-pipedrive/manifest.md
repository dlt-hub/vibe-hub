# Overview

This document provides a configuration guide for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various data resources, each with specific endpoints and configurations. The API uses an API key for authentication and supports incremental synchronization based on datetime fields.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "api_key",
            "name": "api_token",
            "api_key": dlt.secrets["api_token"],
            "location": "query"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "deals",
            "endpoint": {
                "path": "/v1/recents",
                "data_selector": "data.*",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "start",
                    "cursor_path": "additional_data.pagination.next_start",
                    "limit": 50
                }
            }
        },
        {
            "name": "deal_fields",
            "endpoint": {
                "path": "/v1/dealFields",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "start",
                    "cursor_path": "additional_data.pagination.next_start",
                    "limit": 50
                }
            }
        },
        {
            "name": "goals",
            "endpoint": {
                "path": "/v1/goals/find",
                "data_selector": "data.goals"
            }
        },
        {
            "name": "files",
            "endpoint": {
                "path": "/v1/recents",
                "data_selector": "data.*",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "start",
                    "cursor_path": "additional_data.pagination.next_start",
                    "limit": 50
                }
            }
        },
        {
            "name": "filters",
            "endpoint": {
                "path": "/v1/recents",
                "data_selector": "data.*",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "start",
                    "cursor_path": "additional_data.pagination.next_start",
                    "limit": 50
                }
            }
        },
        {
            "name": "lead_labels",
            "endpoint": {
                "path": "/v1/leadLabels",
                "data_selector": "data"
            }
        },
        {
            "name": "leads",
            "endpoint": {
                "path": "/v1/leads",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "start",
                    "cursor_path": "additional_data.pagination.next_start",
                    "limit": 50
                }
            }
        },
        {
            "name": "notes",
            "endpoint": {
                "path": "/v1/recents",
                "data_selector": "data.*",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "start",
                    "cursor_path": "additional_data.pagination.next_start",
                    "limit": 50
                }
            }
        },
        {
            "name": "activities",
            "endpoint": {
                "path": "/v1/recents",
                "data_selector": "data.*",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "start",
                    "cursor_path": "additional_data.pagination.next_start",
                    "limit": 50
                }
            }
        },
        {
            "name": "activity_types",
            "endpoint": {
                "path": "/v1/activityTypes",
                "data_selector": "data"
            }
        },
        {
            "name": "activity_fields",
            "endpoint": {
                "path": "/v1/activityFields",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "start",
                    "cursor_path": "additional_data.pagination.next_start",
                    "limit": 50
                }
            }
        },
        {
            "name": "currencies",
            "endpoint": {
                "path": "/v1/currencies",
                "data_selector": "data"
            }
        },
        {
            "name": "mail",
            "endpoint": {
                "path": "/v1/mailbox/mailThreads/{{ stream_partition.thread_id }}/mailMessages",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "start",
                    "cursor_path": "additional_data.pagination.next_start",
                    "limit": 50
                }
            }
        },
        {
            "name": "organizations",
            "endpoint": {
                "path": "/v1/organizations",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "start",
                    "cursor_path": "additional_data.pagination.next_start",
                    "limit": 50
                }
            }
        },
        {
            "name": "organization_fields",
            "endpoint": {
                "path": "/v1/organizationFields",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "start",
                    "cursor_path": "additional_data.pagination.next_start",
                    "limit": 50
                }
            }
        },
        {
            "name": "permission_sets",
            "endpoint": {
                "path": "/v1/permissionSets",
                "data_selector": "data"
            }
        },
        {
            "name": "persons",
            "endpoint": {
                "path": "/v1/recents",
                "data_selector": "data.*",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "start",
                    "cursor_path": "additional_data.pagination.next_start",
                    "limit": 50
                }
            }
        },
        {
            "name": "person_fields",
            "endpoint": {
                "path": "/v1/personFields",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "start",
                    "cursor_path": "additional_data.pagination.next_start",
                    "limit": 50
                }
            }
        },
        {
            "name": "pipelines",
            "endpoint": {
                "path": "/v1/recents",
                "data_selector": "data.*",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "start",
                    "cursor_path": "additional_data.pagination.next_start",
                    "limit": 50
                }
            }
        },
        {
            "name": "products",
            "endpoint": {
                "path": "/v1/recents",
                "data_selector": "data.*",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "start",
                    "cursor_path": "additional_data.pagination.next_start",
                    "limit": 50
                }
            }
        },
        {
            "name": "product_fields",
            "endpoint": {
                "path": "/v1/productFields",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "start",
                    "cursor_path": "additional_data.pagination.next_start",
                    "limit": 50
                }
            }
        },
        {
            "name": "roles",
            "endpoint": {
                "path": "/v1/roles",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "start",
                    "cursor_path": "additional_data.pagination.next_start",
                    "limit": 50
                }
            }
        },
        {
            "name": "stages",
            "endpoint": {
                "path": "/v1/recents",
                "data_selector": "data.*",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "start",
                    "cursor_path": "additional_data.pagination.next_start",
                    "limit": 50
                }
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/v1/recents",
                "data_selector": "data.*",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "start",
                    "cursor_path": "additional_data.pagination.next_start",
                    "limit": 50
                }
            }
        },
        {
            "name": "deal_products",
            "endpoint": {
                "path": "/v1/deals/{{ stream_slice.parent_id }}/products",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "start",
                    "cursor_path": "additional_data.pagination.next_start",
                    "limit": 50
                }
            }
        },
        {
            "name": "mailThreads",
            "endpoint": {
                "path": "/v1/mailbox/mailThreads",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "start",
                    "cursor_path": "additional_data.pagination.next_start",
                    "limit": 50
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: `api_key`
- **Secrets**: Use `dlt.secrets["api_token"]` to securely store and access the API key.
- **Location**: The API key is included in the query parameters of each request.

# Resources

Each resource corresponds to a specific endpoint and data structure. The following are key configurations for each resource:

- **Resource Name**: A neutral name representing the data being accessed.
- **Endpoint Path**: The path to the resource on the API.
- **Data Selector**: The JSON path to extract data from the API response.
- **Pagination Strategy**: Uses cursor-based pagination with a `start` parameter and a cursor path to the next page token.
- **Primary Key**: The unique identifier for records in the resource.

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: Set a maximum of 3 retries with an exponential backoff factor of 2.

# Schema

The schema for each resource is defined with neutral field names and types. Fields are nullable and follow standard JSON schema conventions. For example, `id` is an integer, and `update_time` is a string formatted as a date-time.