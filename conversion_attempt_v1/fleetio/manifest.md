# Overview

This document provides a configuration guide for integrating with a third-party fleet management API using dltHub's REST API source format. The integration supports data synchronization for various resources such as inspection forms, issues, service entries, vehicles, and more. The API uses cursor-based pagination and requires authentication via API key and account token.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "api_key",
            "name": "api_key",
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
            "name": "inspection_forms",
            "endpoint": {
                "path": "/v1/submitted_inspection_forms",
                "data_selector": "records",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "start_cursor",
                    "cursor_path": "next_cursor"
                }
            }
        },
        {
            "name": "issues",
            "endpoint": {
                "path": "/v2/issues",
                "data_selector": "records",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "start_cursor",
                    "cursor_path": "next_cursor"
                }
            }
        },
        {
            "name": "service_entries",
            "endpoint": {
                "path": "/v2/service_entries",
                "data_selector": "records",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "start_cursor",
                    "cursor_path": "next_cursor"
                }
            }
        },
        {
            "name": "vehicles",
            "endpoint": {
                "path": "/v1/vehicles",
                "data_selector": "records",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "start_cursor",
                    "cursor_path": "next_cursor"
                }
            }
        },
        {
            "name": "expense_entries",
            "endpoint": {
                "path": "/v1/expense_entries",
                "data_selector": "records",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "start_cursor",
                    "cursor_path": "next_cursor"
                }
            }
        },
        {
            "name": "contacts",
            "endpoint": {
                "path": "/v2/contacts",
                "data_selector": "records",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "start_cursor",
                    "cursor_path": "next_cursor"
                }
            }
        },
        {
            "name": "fuel_entries",
            "endpoint": {
                "path": "/v1/fuel_entries",
                "data_selector": "records",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "start_cursor",
                    "cursor_path": "next_cursor"
                }
            }
        },
        {
            "name": "parts",
            "endpoint": {
                "path": "/v1/parts",
                "data_selector": "records",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "start_cursor",
                    "cursor_path": "next_cursor"
                }
            }
        },
        {
            "name": "purchase_orders",
            "endpoint": {
                "path": "/v1/purchase_orders",
                "data_selector": "records",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "start_cursor",
                    "cursor_path": "next_cursor"
                }
            }
        },
        {
            "name": "vehicle_assignments",
            "endpoint": {
                "path": "/v1/vehicle_assignments",
                "data_selector": "records",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "start_cursor",
                    "cursor_path": "next_cursor"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: API Key
- **Secrets**: 
  - `api_key`: The API key for authentication.
  - `account_token`: The account token for authentication.

## Resources

### Inspection Forms
- **Endpoint Path**: `/v1/submitted_inspection_forms`
- **Pagination**: Cursor-based
- **Data Selector**: `records`

### Issues
- **Endpoint Path**: `/v2/issues`
- **Pagination**: Cursor-based
- **Data Selector**: `records`

### Service Entries
- **Endpoint Path**: `/v2/service_entries`
- **Pagination**: Cursor-based
- **Data Selector**: `records`

### Vehicles
- **Endpoint Path**: `/v1/vehicles`
- **Pagination**: Cursor-based
- **Data Selector**: `records`

### Expense Entries
- **Endpoint Path**: `/v1/expense_entries`
- **Pagination**: Cursor-based
- **Data Selector**: `records`

### Contacts
- **Endpoint Path**: `/v2/contacts`
- **Pagination**: Cursor-based
- **Data Selector**: `records`

### Fuel Entries
- **Endpoint Path**: `/v1/fuel_entries`
- **Pagination**: Cursor-based
- **Data Selector**: `records`

### Parts
- **Endpoint Path**: `/v1/parts`
- **Pagination**: Cursor-based
- **Data Selector**: `records`

### Purchase Orders
- **Endpoint Path**: `/v1/purchase_orders`
- **Pagination**: Cursor-based
- **Data Selector**: `records`

### Vehicle Assignments
- **Endpoint Path**: `/v1/vehicle_assignments`
- **Pagination**: Cursor-based
- **Data Selector**: `records`

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

Each resource has a schema defined with fields, types, and nullability. Field names are generalized to ensure neutrality and compatibility with various data models.