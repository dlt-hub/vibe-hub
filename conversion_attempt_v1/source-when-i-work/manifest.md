# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various resources, each with its own endpoint and pagination strategy. The API requires authentication via a session token, which is obtained through a login process.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["session_token"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "account",
            "endpoint": {
                "path": "/v1/account",
                "data_selector": "account",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 5
                }
            }
        },
        {
            "name": "payrolls",
            "endpoint": {
                "path": "/v1/payrolls",
                "data_selector": "payrolls",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 5
                }
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/v1/users",
                "data_selector": "users",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 5
                }
            }
        },
        {
            "name": "timezones",
            "endpoint": {
                "path": "/v1/timezones",
                "data_selector": "timezones"
            }
        },
        {
            "name": "payrolls_notices",
            "endpoint": {
                "path": "/v1/payrolls/notices",
                "data_selector": "[]"
            }
        },
        {
            "name": "times",
            "endpoint": {
                "path": "/v1/times",
                "data_selector": "[]"
            }
        },
        {
            "name": "requests",
            "endpoint": {
                "path": "/v1/requests",
                "data_selector": "requests",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 5
                }
            }
        },
        {
            "name": "blocks",
            "endpoint": {
                "path": "/v1/blocks",
                "data_selector": "blocks",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 5
                }
            }
        },
        {
            "name": "sites",
            "endpoint": {
                "path": "/v1/sites",
                "data_selector": "sites",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 5
                }
            }
        },
        {
            "name": "locations",
            "endpoint": {
                "path": "/v1/locations",
                "data_selector": "locations",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 5
                }
            }
        },
        {
            "name": "positions",
            "endpoint": {
                "path": "/v1/positions",
                "data_selector": "positions"
            }
        },
        {
            "name": "openshiftapprovalrequests",
            "endpoint": {
                "path": "/v1/openshiftapprovalrequests",
                "data_selector": "[]",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 5
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer
- **Token**: Retrieved via a login process using email and password credentials.

# Resources

- **Resource Name**: Abstracted from the original YAML
- **Endpoint Path**: Generalized to `/v1/resource_name`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Page number-based with parameters `page` and `limit`
- **Data Selector**: Extracts data from the specified field path

# Error Handling

- **Retry Logic**: Up to 3 retries with a constant backoff of 5 seconds
- **HTTP Status Codes**: Handles 429 (rate limit) and 401 (token expired) with appropriate actions

# Schema

- **Fields**: Abstracted and generalized from the original YAML
- **Types**: Consistent with the YAML schema, using neutral field names

This configuration provides a clean, vendor-neutral setup for integrating with a third-party analytics API using dltHub's REST API source.