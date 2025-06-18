# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports various data resources, each with specific endpoints and pagination strategies. Authentication is handled via API key or OAuth, ensuring secure access to the API.

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
            "name": "campaigns",
            "endpoint": {
                "path": "/v1/campaigns",
                "data_selector": "campaigns",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "companies",
            "endpoint": {
                "path": "/v1/companies",
                "data_selector": "data.companies",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "page_size",
                    "limit": 20
                }
            }
        },
        {
            "name": "customers",
            "endpoint": {
                "path": "/v1/nps/customers",
                "data_selector": "data.subscribers",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "page_size",
                    "limit": 20
                }
            }
        },
        {
            "name": "feedback",
            "endpoint": {
                "path": "/v1/feedback",
                "data_selector": "data.responses",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "page_size",
                    "limit": 10
                }
            }
        },
        {
            "name": "outbox",
            "endpoint": {
                "path": "/v1/nps/outbox",
                "data_selector": "data.surveys",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "page_size",
                    "limit": 20
                }
            }
        },
        {
            "name": "reports",
            "endpoint": {
                "path": "/v1/reports",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "nps",
            "endpoint": {
                "path": "/v1/nps/score",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "templates",
            "endpoint": {
                "path": "/v1/templates",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "page_size",
                    "limit": 20
                }
            }
        }
    ]
})
```

## Authentication

The API supports two types of authentication:

- **API Key Authentication**: The API key is included in the request header.
- **OAuth Authentication**: Utilizes client credentials to obtain a token from the token refresh endpoint.

### Secrets or Config Values

- `api_key`: The API key for accessing the API.
- `client_id`: The client ID for OAuth authentication.
- `client_secret`: The client secret for OAuth authentication.
- `refresh_token`: The refresh token for obtaining new access tokens.

## Resources

### Campaigns

- **Endpoint Path**: `/v1/campaigns`
- **Data Selector**: `campaigns`
- **Pagination**: Single page

### Companies

- **Endpoint Path**: `/v1/companies`
- **Data Selector**: `data.companies`
- **Pagination**: Page number with a limit of 20

### Customers

- **Endpoint Path**: `/v1/nps/customers`
- **Data Selector**: `data.subscribers`
- **Pagination**: Page number with a limit of 20

### Feedback

- **Endpoint Path**: `/v1/feedback`
- **Data Selector**: `data.responses`
- **Pagination**: Page number with a limit of 10

### Outbox

- **Endpoint Path**: `/v1/nps/outbox`
- **Data Selector**: `data.surveys`
- **Pagination**: Page number with a limit of 20

### Reports

- **Endpoint Path**: `/v1/reports`
- **Data Selector**: `data`
- **Pagination**: Single page

### NPS

- **Endpoint Path**: `/v1/nps/score`
- **Data Selector**: `data`
- **Pagination**: Single page

### Templates

- **Endpoint Path**: `/v1/templates`
- **Data Selector**: `data`
- **Pagination**: Page number with a limit of 20

## Error Handling

- **Retry Logic**: The API will retry requests on status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

The schema for each resource is defined with neutral field names and types, ensuring compatibility with various data structures. Each resource's schema includes fields such as `id`, `name`, and other relevant attributes, with appropriate data types and descriptions.