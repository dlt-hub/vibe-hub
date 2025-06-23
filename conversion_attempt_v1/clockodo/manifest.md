# Overview

This document provides a configuration guide for integrating with a third-party time tracking and project management API using dltHub's REST API source. The integration allows for the extraction of various data types such as projects, absences, customers, and more, facilitating data-driven insights and decision-making.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["email_address"],
            "password": dlt.secrets["api_key"]
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
                "path": "/v2/projects",
                "data_selector": "projects",
                "paginator": {
                    "type": "cursor",
                    "limit": 1000,
                    "cursor_param": "page",
                    "stop_condition": "response.paging.count_pages == response.paging.current_page"
                }
            }
        },
        {
            "name": "absences",
            "endpoint": {
                "path": "/v2/absences",
                "data_selector": "absences",
                "partition_router": {
                    "type": "list",
                    "values": dlt.config["years"],
                    "cursor_field": "year",
                    "request_option": {
                        "inject_into": "request_parameter",
                        "field_name": "year"
                    }
                }
            }
        },
        {
            "name": "customers",
            "endpoint": {
                "path": "/v2/customers",
                "data_selector": "customers",
                "paginator": {
                    "type": "cursor",
                    "limit": 1000,
                    "cursor_param": "page",
                    "stop_condition": "response.paging.count_pages == response.paging.current_page"
                }
            }
        },
        {
            "name": "entries",
            "endpoint": {
                "path": "/v2/entries",
                "data_selector": "entries",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "page",
                    "stop_condition": "response.paging.count_pages == response.paging.current_page"
                },
                "incremental_sync": {
                    "type": "datetime",
                    "cursor_field": "time_last_change",
                    "start_datetime": dlt.config["start_date"],
                    "end_datetime": "now"
                }
            }
        }
        // Additional resources can be added here following the same pattern
    ]
})
```

## Authentication

- **Type**: Basic Authentication
- **Secrets**: 
  - `email_address`: Your account email address.
  - `api_key`: Your API key for authentication.

## Resources

### Projects
- **Endpoint Path**: `/v2/projects`
- **Data Selector**: `projects`
- **Pagination**: Cursor-based with `page` as the cursor parameter and a limit of 1000 items per page.

### Absences
- **Endpoint Path**: `/v2/absences`
- **Data Selector**: `absences`
- **Partitioning**: Year-based partitioning using a list of years.

### Customers
- **Endpoint Path**: `/v2/customers`
- **Data Selector**: `customers`
- **Pagination**: Cursor-based with `page` as the cursor parameter and a limit of 1000 items per page.

### Entries
- **Endpoint Path**: `/v2/entries`
- **Data Selector**: `entries`
- **Pagination**: Cursor-based with `page` as the cursor parameter.
- **Incremental Sync**: Based on `time_last_change` field.

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504 with a maximum of 3 retries and a backoff factor of 2.

## Schema

- **Projects**: Includes fields like `id`, `name`, `active`, etc.
- **Absences**: Includes fields like `id`, `type`, `date_since`, etc.
- **Customers**: Includes fields like `id`, `name`, `active`, etc.
- **Entries**: Includes fields like `id`, `time_last_change`, `duration`, etc.

Each schema is defined with neutral field names and types, ensuring compatibility with various data warehouses.