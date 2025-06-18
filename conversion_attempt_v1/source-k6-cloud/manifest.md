# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports data synchronization from various resources such as organizations, projects, and tests. The API requires authentication via a bearer token and supports pagination for data retrieval.

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
            "name": "organizations",
            "endpoint": {
                "path": "/v3/organizations",
                "data_selector": "organizations"
            }
        },
        {
            "name": "projects",
            "endpoint": {
                "path": "/v3/organizations/{organization_id}/projects",
                "data_selector": "projects",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "page_size",
                    "limit": 32
                }
            }
        },
        {
            "name": "tests",
            "endpoint": {
                "path": "/loadtests/v2/tests",
                "data_selector": "tests",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "page_size",
                    "limit": 32
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer Token
- **Token**: Stored securely in `dlt.secrets["api_token"]`

# Resources

## Organizations

- **Endpoint Path**: `/v3/organizations`
- **Data Selector**: `organizations`
- **Primary Key**: `id`

## Projects

- **Endpoint Path**: `/v3/organizations/{organization_id}/projects`
- **Data Selector**: `projects`
- **Pagination Strategy**: Page Number
  - **Page Parameter**: `page`
  - **Limit Parameter**: `page_size`
  - **Limit**: 32

## Tests

- **Endpoint Path**: `/loadtests/v2/tests`
- **Data Selector**: `tests`
- **Pagination Strategy**: Page Number
  - **Page Parameter**: `page`
  - **Limit Parameter**: `page_size`
  - **Limit**: 32

# Error Handling

- **Retry Logic**: Implemented for HTTP status codes 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

## Organizations

- **Fields**:
  - `billing_address`: string
  - `billing_country`: string
  - `billing_email`: string
  - `created`: string
  - `description`: string
  - `id`: integer
  - `is_default`: boolean
  - `is_saml_org`: boolean
  - `name`: string
  - `owner_id`: integer
  - `updated`: string
  - `vat_number`: string

## Projects

- **Fields**:
  - `created`: string
  - `description`: string
  - `id`: integer
  - `is_default`: boolean
  - `name`: string
  - `organization_id`: integer
  - `updated`: string

## Tests

- **Fields**:
  - `created`: string
  - `id`: integer
  - `last_test_run_id`: string
  - `name`: string
  - `project_id`: integer
  - `script`: string
  - `test_run_ids`: array
  - `updated`: string
  - `user_id`: integer

This configuration provides a clean and vendor-neutral setup for integrating with a third-party analytics API using dltHub's REST API source format.