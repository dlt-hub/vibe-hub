# Overview

This document provides a configuration example for integrating with a third-party customer relationship management (CRM) API using dltHub's REST API source. The integration allows for seamless data synchronization of CRM data, enabling efficient tracking of customer information, interactions, and leads. This setup helps businesses centralize CRM data for enhanced analysis and insights.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v2",
        "auth": {
            "type": "api_key",
            "name": "Authorization",
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
            "name": "users",
            "endpoint": {
                "path": "/",
                "method": "POST",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "Page",
                    "limit_param": "MaxNumberOfResults",
                    "limit": 1000,
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "contacts",
            "endpoint": {
                "path": "/",
                "method": "POST",
                "data_selector": "Results",
                "paginator": {
                    "type": "page_number",
                    "page_param": "Page",
                    "limit_param": "MaxNumberOfResults",
                    "limit": 1000,
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "tasks",
            "endpoint": {
                "path": "/",
                "method": "POST",
                "data_selector": "Results.*",
                "paginator": {
                    "type": "page_number",
                    "page_param": "Page",
                    "limit_param": "MaxNumberOfResults",
                    "limit": 500,
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "pipeline_items",
            "endpoint": {
                "path": "/",
                "method": "POST",
                "data_selector": "PipelineItems",
                "paginator": {
                    "type": "page_number",
                    "page_param": "Page",
                    "limit_param": "MaxNumberOfResults",
                    "limit": 500,
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "notes",
            "endpoint": {
                "path": "/",
                "method": "POST",
                "data_selector": "Results",
                "paginator": {
                    "type": "page_number",
                    "page_param": "Page",
                    "limit_param": "MaxNumberOfResults",
                    "limit": 1000,
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "teams",
            "endpoint": {
                "path": "/",
                "method": "POST",
                "data_selector": "data"
            }
        },
        {
            "name": "events",
            "endpoint": {
                "path": "/",
                "method": "POST",
                "data_selector": "Results",
                "paginator": {
                    "type": "page_number",
                    "page_param": "Page",
                    "limit_param": "MaxNumberOfResults",
                    "limit": 500,
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "contact_events",
            "endpoint": {
                "path": "/",
                "method": "POST",
                "data_selector": "Results",
                "paginator": {
                    "type": "page_number",
                    "page_param": "Page",
                    "limit_param": "MaxNumberOfResults",
                    "limit": 1000,
                    "start_from_page": 1
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets**: Use `dlt.secrets["api_key"]` to securely manage your API key.
- **Location**: The API key is included in the request header under the name `Authorization`.

## Resources

### Users
- **Endpoint Path**: `/`
- **HTTP Method**: POST
- **Data Selector**: `data`
- **Pagination**: Page number with parameters `Page` and `MaxNumberOfResults`, starting from page 1 with a limit of 1000.

### Contacts
- **Endpoint Path**: `/`
- **HTTP Method**: POST
- **Data Selector**: `Results`
- **Pagination**: Page number with parameters `Page` and `MaxNumberOfResults`, starting from page 1 with a limit of 1000.

### Tasks
- **Endpoint Path**: `/`
- **HTTP Method**: POST
- **Data Selector**: `Results.*`
- **Pagination**: Page number with parameters `Page` and `MaxNumberOfResults`, starting from page 1 with a limit of 500.

### Pipeline Items
- **Endpoint Path**: `/`
- **HTTP Method**: POST
- **Data Selector**: `PipelineItems`
- **Pagination**: Page number with parameters `Page` and `MaxNumberOfResults`, starting from page 1 with a limit of 500.

### Notes
- **Endpoint Path**: `/`
- **HTTP Method**: POST
- **Data Selector**: `Results`
- **Pagination**: Page number with parameters `Page` and `MaxNumberOfResults`, starting from page 1 with a limit of 1000.

### Teams
- **Endpoint Path**: `/`
- **HTTP Method**: POST
- **Data Selector**: `data`

### Events
- **Endpoint Path**: `/`
- **HTTP Method**: POST
- **Data Selector**: `Results`
- **Pagination**: Page number with parameters `Page` and `MaxNumberOfResults`, starting from page 1 with a limit of 500.

### Contact Events
- **Endpoint Path**: `/`
- **HTTP Method**: POST
- **Data Selector**: `Results`
- **Pagination**: Page number with parameters `Page` and `MaxNumberOfResults`, starting from page 1 with a limit of 1000.

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

The schema for each resource is defined with neutral field names and types, ensuring compatibility with various data warehouses and analytics tools. Each resource's schema is consistent with the provided YAML configuration, using generic field names like `id`, `name`, and `date_created`.