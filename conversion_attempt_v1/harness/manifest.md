# Overview

This document provides a configuration example for integrating with a third-party analytics API using dltHub's REST API source format. The integration allows for the retrieval of organizational data through a RESTful API endpoint. The configuration supports API key-based authentication and includes pagination and error handling strategies.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "api_key",
            "name": "x-api-key",
            "api_key": dlt.secrets["api_key"],
            "location": "header"
        }
    },
    "resource_defaults": {
        "primary_key": "identifier",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "organizations",
            "endpoint": {
                "path": "/v1/organizations",
                "data_selector": ["data", "content"],
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [429, 500, 502, 503, 504],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: API Key
- **Header Name**: `x-api-key`
- **Secrets**: The API key is stored securely using `dlt.secrets["api_key"]`.

# Resources

- **Resource Name**: organizations
- **Endpoint Path**: `/v1/organizations`
- **HTTP Method**: GET
- **Pagination Strategy**: Single page (no pagination)
- **Data Selector**: `["data", "content"]`

# Error Handling

- **Retry Logic**: The configuration includes retry logic for handling transient errors.
  - **Retry on Status Codes**: 429, 500, 502, 503, 504
  - **Max Retries**: 3
  - **Backoff Factor**: 2

# Schema

The schema for the `organizations` resource includes the following fields:

- **createdAt**: number or null
- **harnessManaged**: boolean or null
- **lastModifiedAt**: number or null
- **organization**: object or null
  - **description**: string or null
  - **identifier**: string or null
  - **name**: string or null
  - **tags**: object or null
    - **identifier**: string or null

This configuration provides a clean and generalized setup for accessing organizational data from a third-party analytics API using dltHub's REST API source format.