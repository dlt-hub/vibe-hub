# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source format. The integration supports various resources, each with its own endpoint and configuration settings. The API uses basic authentication and supports pagination for data retrieval.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["api_key"]
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
                "path": "/v1/user",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 1000,
                    "offset_param": "_skip"
                }
            }
        },
        {
            "name": "lead_custom_fields",
            "endpoint": {
                "path": "/v1/custom_field/lead",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 1000,
                    "offset_param": "_skip"
                }
            }
        },
        {
            "name": "contact_custom_fields",
            "endpoint": {
                "path": "/v1/custom_field/contact",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 1000,
                    "offset_param": "_skip"
                }
            }
        },
        {
            "name": "opportunity_custom_fields",
            "endpoint": {
                "path": "/v1/custom_field/opportunity",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 1000,
                    "offset_param": "_skip"
                }
            }
        },
        {
            "name": "activity_custom_fields",
            "endpoint": {
                "path": "/v1/custom_field/activity",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 1000,
                    "offset_param": "_skip"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Basic
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access the API key.

# Resources

Each resource is configured with a unique endpoint path and supports pagination using an offset strategy. The data selector is set to "data" to extract the relevant information from the API response.

- **Resource Name**: Abstracted to a neutral term (e.g., "users", "lead_custom_fields").
- **Endpoint Path**: Generalized to `/v1/resource_path`.
- **Pagination Strategy**: Offset with parameters for limit and offset.

# Error Handling

The configuration does not explicitly define error handling, but it is recommended to implement retry logic for common HTTP status codes such as 429, 500, 502, 503, and 504. This can be achieved using a backoff strategy with a maximum number of retries.

# Schema

The schema for each resource is defined with neutral field names and types. Fields are marked as required based on the presence of primary keys in the configuration. The schema supports additional properties to accommodate any extra fields returned by the API.