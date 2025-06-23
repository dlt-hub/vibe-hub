# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various data resources, each with its own endpoint and pagination strategy. The API uses OAuth2 authentication for secure access.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "oauth2",
            "client_id": dlt.secrets["client_id"],
            "client_secret": dlt.secrets["client_secret"],
            "token_url": "https://api.example.com/authenticate/"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "buyers",
            "endpoint": {
                "path": "/buyers",
                "data_selector": "results",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "page_size"
                }
            }
        },
        {
            "name": "listings",
            "endpoint": {
                "path": "/listings",
                "data_selector": "results",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "page_size"
                }
            }
        },
        {
            "name": "contracts",
            "endpoint": {
                "path": "/contracts",
                "data_selector": "results",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "page_size"
                }
            }
        },
        {
            "name": "opportunities",
            "endpoint": {
                "path": "/opportunities",
                "data_selector": "results",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "page_size"
                }
            }
        },
        {
            "name": "private_offers",
            "endpoint": {
                "path": "/private_offers",
                "data_selector": "results",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "page_size"
                }
            }
        },
        {
            "name": "analytics_marketplace_disbursements",
            "endpoint": {
                "path": "/analytics/datasets/marketplace_disbursements",
                "data_selector": "results",
                "paginator": {
                    "type": "page_number",
                    "limit": 5000,
                    "page_param": "page",
                    "limit_param": "page_size"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: OAuth2
- **Client ID**: Retrieved from `dlt.secrets["client_id"]`
- **Client Secret**: Retrieved from `dlt.secrets["client_secret"]`
- **Token URL**: `https://api.example.com/authenticate/`

# Resources

Each resource is configured with a specific endpoint path, data selector, and pagination strategy. The primary key for all resources is `id`, and the write disposition is set to `merge`.

- **Resource Name**: buyers
  - **Endpoint Path**: `/buyers`
  - **Pagination**: Page number with `page` and `page_size` parameters

- **Resource Name**: listings
  - **Endpoint Path**: `/listings`
  - **Pagination**: Page number with `page` and `page_size` parameters

- **Resource Name**: contracts
  - **Endpoint Path**: `/contracts`
  - **Pagination**: Page number with `page` and `page_size` parameters

- **Resource Name**: opportunities
  - **Endpoint Path**: `/opportunities`
  - **Pagination**: Page number with `page` and `page_size` parameters

- **Resource Name**: private_offers
  - **Endpoint Path**: `/private_offers`
  - **Pagination**: Page number with `page` and `page_size` parameters

- **Resource Name**: analytics_marketplace_disbursements
  - **Endpoint Path**: `/analytics/datasets/marketplace_disbursements`
  - **Pagination**: Page number with `page` and `page_size` parameters

# Error Handling

The configuration does not explicitly define error handling strategies. It is recommended to implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504, with a maximum of 3 retries and an exponential backoff factor of 2.

# Schema

The schema for each resource is defined with required fields and types. Field names are generalized to ensure vendor neutrality. For example, `id` and `last_modified_at` are common fields across resources. Additional properties are allowed to accommodate any extra fields returned by the API.