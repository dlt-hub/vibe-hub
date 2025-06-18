# Overview

This document provides a configuration example for integrating with a third-party CRM API using the dltHub REST API source. The integration allows for data extraction from various resources such as accounts, contacts, events, products, and pipelines. The configuration is designed to be vendor-neutral and follows the dltHub REST API source format.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["username"],
            "password": dlt.secrets["password"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "accounts",
            "endpoint": {
                "path": "/accounts",
                "data_selector": "accounts",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page[page]",
                    "limit_param": "page[limit]",
                    "limit": 500
                }
            }
        },
        {
            "name": "contacts",
            "endpoint": {
                "path": "/contacts",
                "data_selector": "contacts",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page[page]",
                    "limit_param": "page[limit]",
                    "limit": 500
                }
            }
        },
        {
            "name": "events",
            "endpoint": {
                "path": "/events",
                "data_selector": "events",
                "paginator": {
                    "type": "page_number",
                    "limit_param": "limit",
                    "limit": 500
                }
            }
        }
        // Additional resources can be added here following the same pattern
    ]
})
```

# Authentication

- **Type**: Basic Authentication
- **Secrets**: 
  - `username`: The username for API access.
  - `password`: The password or API token for authentication.

# Resources

Each resource is configured with the following parameters:

- **Resource Name**: A neutral name representing the data being accessed.
- **Endpoint Path**: The API path to access the resource.
- **Data Selector**: The JSON path to extract data from the API response.
- **Pagination Strategy**: 
  - **Type**: `page_number`
  - **Parameters**: 
    - `page_param`: The query parameter for the page number.
    - `limit_param`: The query parameter for the page size.
    - `limit`: The maximum number of records per page.

# Error Handling

The configuration includes basic error handling strategies:

- **Retry Logic**: 
  - Retry on HTTP status codes 429, 500, 502, 503, 504.
  - Maximum retries: 3
  - Backoff factor: 2

# Schema

The schema for each resource is defined with neutral field names and types. The primary key for each resource is `id`, ensuring uniqueness and consistency across data extractions. The schema is designed to be flexible, allowing for additional properties as needed.