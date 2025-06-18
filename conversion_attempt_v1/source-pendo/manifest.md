# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration allows for the retrieval of various resources such as pages, features, reports, guides, account metadata, and visitor metadata. The API supports authentication via API key and uses a simple pagination strategy.

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
            "name": "page",
            "endpoint": {
                "path": "/v1/page",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "feature",
            "endpoint": {
                "path": "/v1/feature",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "report",
            "endpoint": {
                "path": "/v1/report",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "guide",
            "endpoint": {
                "path": "/v1/guide",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "account_metadata",
            "endpoint": {
                "path": "/v1/metadata/schema/account",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "visitor_metadata",
            "endpoint": {
                "path": "/v1/metadata/schema/visitor",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: Use `dlt.secrets["api_key"]` to securely store and access the API key.
- **Location**: The API key is included in the request header.

# Resources

- **Resource Name**: Abstracted names such as `page`, `feature`, `report`, `guide`, `account_metadata`, and `visitor_metadata`.
- **Endpoint Path and Method**: Each resource has a specific endpoint path, and the default HTTP method is GET.
- **Pagination Strategy**: All resources use a `single_page` pagination strategy.
- **Data Selector**: The data is extracted from the `data` field in the response.

# Error Handling

- **Retry/Backoff Logic**: Not explicitly defined in the YAML, but can be configured as needed.
- **Known HTTP Response Codes**: Not specified, but common codes like 429, 500, 502, 503, and 504 can be handled with retries.
- **Fallback/Handling Behavior**: Generalized error handling can be implemented as needed.

# Schema

- **Fields, Types, Nullability**: The schema for each resource is defined with fields such as `id`, `name`, `createdAt`, etc., with appropriate data types and descriptions.
- **Neutral Field Names**: Field names are generalized and do not include any proprietary or branded terms.