# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various data resources, each with its own endpoint and configuration settings. The API uses bearer token authentication and supports both full and incremental data synchronization.

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
            "name": "organizations",
            "endpoint": {
                "path": "/v2/organizations",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 20,
                    "offset_param": "after"
                }
            }
        },
        {
            "name": "policies",
            "endpoint": {
                "path": "/v2/policies",
                "data_selector": "data"
            },
            "incremental_sync": {
                "cursor_field": "updated",
                "start_datetime": dlt.config["start_date"],
                "datetime_format": "%Y-%m-%dT%H:%M:%SZ"
            }
        },
        {
            "name": "activities",
            "endpoint": {
                "path": "/v2/activities",
                "data_selector": "activities",
                "paginator": {
                    "type": "offset",
                    "limit": 20,
                    "offset_param": "after"
                }
            },
            "incremental_sync": {
                "cursor_field": "activityTime",
                "start_datetime": dlt.config["start_date"],
                "datetime_format": "%Y-%m-%dT%H:%M:%SZ"
            }
        },
        {
            "name": "automation_scripts",
            "endpoint": {
                "path": "/v2/automation/scripts",
                "data_selector": "data"
            },
            "incremental_sync": {
                "cursor_field": "updatedOn",
                "start_datetime": dlt.config["start_date"],
                "datetime_format": "%Y-%m-%dT%H:%M:%SZ"
            }
        },
        {
            "name": "groups",
            "endpoint": {
                "path": "/v2/groups",
                "data_selector": "data"
            },
            "incremental_sync": {
                "cursor_field": "updated",
                "start_datetime": dlt.config["start_date"],
                "datetime_format": "%Y-%m-%dT%H:%M:%SZ"
            }
        },
        {
            "name": "locations",
            "endpoint": {
                "path": "/v2/locations",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 20,
                    "offset_param": "after"
                }
            }
        },
        {
            "name": "roles",
            "endpoint": {
                "path": "/v2/roles",
                "data_selector": "data"
            },
            "incremental_sync": {
                "cursor_field": "created",
                "start_datetime": dlt.config["start_date"],
                "datetime_format": "%Y-%m-%dT%H:%M:%SZ"
            }
        },
        {
            "name": "software_products",
            "endpoint": {
                "path": "/v2/software-products",
                "data_selector": "data"
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer
- **Token**: Retrieved from `dlt.secrets["api_key"]`

# Resources

Each resource corresponds to a specific endpoint and may support incremental synchronization based on a datetime cursor field.

- **Organizations**
  - **Endpoint Path**: `/v2/organizations`
  - **Pagination**: Offset with `after` parameter
  - **Primary Key**: `id`

- **Policies**
  - **Endpoint Path**: `/v2/policies`
  - **Incremental Sync**: Based on `updated` field

- **Activities**
  - **Endpoint Path**: `/v2/activities`
  - **Pagination**: Offset with `after` parameter
  - **Incremental Sync**: Based on `activityTime` field

- **Automation Scripts**
  - **Endpoint Path**: `/v2/automation/scripts`
  - **Incremental Sync**: Based on `updatedOn` field

- **Groups**
  - **Endpoint Path**: `/v2/groups`
  - **Incremental Sync**: Based on `updated` field

- **Locations**
  - **Endpoint Path**: `/v2/locations`
  - **Pagination**: Offset with `after` parameter

- **Roles**
  - **Endpoint Path**: `/v2/roles`
  - **Incremental Sync**: Based on `created` field

- **Software Products**
  - **Endpoint Path**: `/v2/software-products`

# Error Handling

- **Retry Logic**: Implement retry on common HTTP status codes such as 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 and a maximum of 3 retries.

# Schema

Each resource has a defined schema with fields, types, and nullability. The primary key for each resource is `id`, and datetime fields are formatted as ISO 8601 strings.