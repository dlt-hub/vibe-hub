# Overview

This document provides a configuration guide for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various resources, each with its own endpoint and pagination strategy. Authentication is handled via an API key, and error handling includes retry logic for rate-limited responses.

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
                "path": "/v1/organizations",
                "data_selector": "data.items",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page_index",
                    "limit_param": "items_per_page",
                    "limit": 20
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "employees",
            "endpoint": {
                "path": "/v1/organizations/{organization_id}/employees",
                "data_selector": "data.items",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page_index",
                    "limit_param": "items_per_page",
                    "limit": 20
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "leave_requests",
            "endpoint": {
                "path": "/v1/organizations/{organization_id}/leave_requests",
                "data_selector": "data.items",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page_index",
                    "limit_param": "items_per_page",
                    "limit": 20
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "employee_certifications",
            "endpoint": {
                "path": "/v1/organizations/{organization_id}/employees/{employee_id}/certifications",
                "data_selector": "data.items",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page_index",
                    "limit_param": "items_per_page",
                    "limit": 20
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "pay_details",
            "endpoint": {
                "path": "/v1/organizations/{organization_id}/employees/{employee_id}/pay_details",
                "data_selector": "data.items",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page_index",
                    "limit_param": "items_per_page",
                    "limit": 20
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "teams",
            "endpoint": {
                "path": "/v1/organizations/{organization_id}/teams",
                "data_selector": "data.items",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page_index",
                    "limit_param": "items_per_page",
                    "limit": 20
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "policies",
            "endpoint": {
                "path": "/v1/organizations/{organization_id}/policies",
                "data_selector": "data.items",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page_index",
                    "limit_param": "items_per_page",
                    "limit": 20
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "certifications",
            "endpoint": {
                "path": "/v1/organizations/{organization_id}/certifications",
                "data_selector": "data.items",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page_index",
                    "limit_param": "items_per_page",
                    "limit": 20
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "custom_fields",
            "endpoint": {
                "path": "/v1/organizations/{organization_id}/custom_fields",
                "data_selector": "data.items",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page_index",
                    "limit_param": "items_per_page",
                    "limit": 20
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "employee_custom_fields",
            "endpoint": {
                "path": "/v1/organizations/{organization_id}/employees/{employee_id}/custom_fields",
                "data_selector": "data.items",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page_index",
                    "limit_param": "items_per_page",
                    "limit": 20
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer
- **Token**: Retrieved from `dlt.secrets["api_key"]`

# Resources

Each resource is configured with a specific endpoint path and pagination strategy. The data selector is set to extract items from the `data.items` path.

- **Organizations**: `/v1/organizations`
- **Employees**: `/v1/organizations/{organization_id}/employees`
- **Leave Requests**: `/v1/organizations/{organization_id}/leave_requests`
- **Employee Certifications**: `/v1/organizations/{organization_id}/employees/{employee_id}/certifications`
- **Pay Details**: `/v1/organizations/{organization_id}/employees/{employee_id}/pay_details`
- **Teams**: `/v1/organizations/{organization_id}/teams`
- **Policies**: `/v1/organizations/{organization_id}/policies`
- **Certifications**: `/v1/organizations/{organization_id}/certifications`
- **Custom Fields**: `/v1/organizations/{organization_id}/custom_fields`
- **Employee Custom Fields**: `/v1/organizations/{organization_id}/employees/{employee_id}/custom_fields`

# Error Handling

- **Retry Logic**: Retries on HTTP status code 429 (rate-limited) with a maximum of 3 retries and an exponential backoff factor of 2.

# Schema

The schema for each resource includes fields such as `id`, `name`, and other relevant attributes. All fields are defined with neutral names and types, ensuring compatibility with the dltHub format.