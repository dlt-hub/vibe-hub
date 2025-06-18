# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various resources, each with its own endpoint and pagination strategy. The API requires authentication via an API key, which is included in the request headers.

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
            "location": "header"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "documents",
            "endpoint": {
                "path": "/v1/document/list",
                "data_selector": "result",
                "paginator": {
                    "type": "page_number",
                    "limit": 50,
                    "page_param": "Page",
                    "limit_param": "pageSize"
                }
            }
        },
        {
            "name": "brands",
            "endpoint": {
                "path": "/v1/brand/list",
                "data_selector": "result",
                "paginator": {
                    "type": "page_number",
                    "limit": 50,
                    "page_param": "Page",
                    "limit_param": "pageSize"
                }
            }
        },
        {
            "name": "sender_identities",
            "endpoint": {
                "path": "/v1/senderIdentities/list",
                "data_selector": "result",
                "paginator": {
                    "type": "page_number",
                    "limit": 50,
                    "page_param": "Page",
                    "limit_param": "pageSize"
                }
            }
        },
        {
            "name": "teams",
            "endpoint": {
                "path": "/v1/teams/list",
                "data_selector": "results",
                "paginator": {
                    "type": "page_number",
                    "limit": 50,
                    "page_param": "Page",
                    "limit_param": "pageSize"
                }
            }
        },
        {
            "name": "templates",
            "endpoint": {
                "path": "/v1/template/list",
                "data_selector": "result",
                "paginator": {
                    "type": "page_number",
                    "limit": 50,
                    "page_param": "Page",
                    "limit_param": "pageSize"
                }
            }
        },
        {
            "name": "users_list",
            "endpoint": {
                "path": "/v1/users/list",
                "data_selector": "result",
                "paginator": {
                    "type": "page_number",
                    "limit": 50,
                    "page_param": "Page",
                    "limit_param": "pageSize"
                }
            }
        },
        {
            "name": "custom_fields",
            "endpoint": {
                "path": "/v1/customField/list",
                "data_selector": "result",
                "paginator": {
                    "type": "page_number",
                    "limit": 50,
                    "page_param": "Page",
                    "limit_param": "pageSize"
                }
            }
        },
        {
            "name": "contacts",
            "endpoint": {
                "path": "/v1/contacts/list",
                "data_selector": "result",
                "paginator": {
                    "type": "page_number",
                    "limit": 50,
                    "page_param": "Page",
                    "limit_param": "pageSize"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: API Key
- **Location**: Header
- **Key Name**: `X-API-KEY`
- **Secret**: `dlt.secrets["api_key"]`

# Resources

Each resource is configured with a specific endpoint path and pagination strategy. The data selector is used to extract the relevant data from the API response.

- **Documents**
  - **Endpoint Path**: `/v1/document/list`
  - **Data Selector**: `result`
  - **Pagination**: Page number with limit 50

- **Brands**
  - **Endpoint Path**: `/v1/brand/list`
  - **Data Selector**: `result`
  - **Pagination**: Page number with limit 50

- **Sender Identities**
  - **Endpoint Path**: `/v1/senderIdentities/list`
  - **Data Selector**: `result`
  - **Pagination**: Page number with limit 50

- **Teams**
  - **Endpoint Path**: `/v1/teams/list`
  - **Data Selector**: `results`
  - **Pagination**: Page number with limit 50

- **Templates**
  - **Endpoint Path**: `/v1/template/list`
  - **Data Selector**: `result`
  - **Pagination**: Page number with limit 50

- **Users List**
  - **Endpoint Path**: `/v1/users/list`
  - **Data Selector**: `result`
  - **Pagination**: Page number with limit 50

- **Custom Fields**
  - **Endpoint Path**: `/v1/customField/list`
  - **Data Selector**: `result`
  - **Pagination**: Page number with limit 50

- **Contacts**
  - **Endpoint Path**: `/v1/contacts/list`
  - **Data Selector**: `result`
  - **Pagination**: Page number with limit 50

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

The schema for each resource is defined with neutral field names and types. The fields are consistent with the data returned by the API and are designed to be flexible and adaptable to changes in the API response structure.