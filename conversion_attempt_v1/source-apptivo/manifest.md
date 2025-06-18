# Overview

This document provides a configuration guide for integrating with a third-party CRM API using dltHub's REST API source. The integration facilitates seamless data synchronization between the CRM and various data warehouses or databases, automating data transfer for analytics, reporting, and insights. The API allows businesses to synchronize CRM data, such as customer information, contacts, and opportunities, with other systems to streamline workflows and improve data accessibility across platforms.

# Configuration Example

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
            "location": "query"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "customers",
            "endpoint": {
                "path": "/v1/customers",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "startIndex"
                }
            }
        },
        {
            "name": "contacts",
            "endpoint": {
                "path": "/v1/contacts",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "startIndex"
                }
            }
        },
        {
            "name": "cases",
            "endpoint": {
                "path": "/v1/cases",
                "data_selector": "data"
            }
        },
        {
            "name": "leads",
            "endpoint": {
                "path": "/v1/leads",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "startIndex"
                }
            }
        },
        {
            "name": "opportunities",
            "endpoint": {
                "path": "/v1/opportunities",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "startIndex"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: 
  - `api_key`: The API key used for authentication, stored securely in dltHub secrets.
  - `access_key`: An additional access key required for API requests, also stored securely.

# Resources

- **Resource Name**: Abstracted from the original stream names (e.g., "customers", "contacts").
- **Endpoint Path and Method**: Each resource has a defined path (e.g., `/v1/customers`) and uses the GET method.
- **Pagination Strategy**: 
  - Type: `offset`
  - Limit: 100
  - Offset Parameter: `startIndex`
- **Data Selector**: The field path to extract data is specified as `data`.

# Error Handling

- **Retry/Backoff Logic**: Implement retry logic for handling transient errors.
- **Known HTTP Response Codes**: Handle common HTTP status codes like 429, 500, 502, 503, and 504 with retries.
- **Fallback/Handling Behavior**: Generalized error handling to ensure robust data synchronization.

# Schema

- **Fields, Types, Nullability**: The schema for each resource is defined with neutral field names and types, ensuring compatibility with various data warehouses.
- **Neutral Field Names**: Use generic field names like `customer_id`, `contact_id`, etc., to maintain vendor neutrality.

This configuration guide ensures a clean, vendor-neutral integration with a third-party CRM API using dltHub's REST API source, facilitating efficient data synchronization and accessibility.