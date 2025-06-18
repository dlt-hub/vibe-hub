# Overview

This document provides a configuration example for integrating with a third-party form management API using the dltHub REST API source. The integration allows for the retrieval of various resources such as forms, reports, questions, and submissions. The API supports both incremental and full data synchronization.

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
            "name": "forms",
            "endpoint": {
                "path": "/v1/forms",
                "data_selector": "content",
                "paginator": {
                    "type": "offset",
                    "limit": 20,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "reports",
            "endpoint": {
                "path": "/v1/reports",
                "data_selector": "content"
            }
        },
        {
            "name": "questions",
            "endpoint": {
                "path": "/v1/forms/{form_id}/questions",
                "data_selector": "content.*"
            }
        },
        {
            "name": "form_files",
            "endpoint": {
                "path": "/v1/forms/{form_id}/files",
                "data_selector": "content"
            }
        },
        {
            "name": "submissions",
            "endpoint": {
                "path": "/v1/submissions",
                "data_selector": "content",
                "paginator": {
                    "type": "offset",
                    "limit": 20,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "form_properties",
            "endpoint": {
                "path": "/v1/forms/{form_id}/properties",
                "data_selector": "content"
            }
        }
    ]
})
```

# Authentication

- **Type**: `api_key`
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access the API key.
- **Location**: The API key is included in the query parameters of each request.

# Resources

- **Forms**: Retrieves form data with pagination support.
- **Reports**: Accesses report data.
- **Questions**: Fetches questions related to specific forms.
- **Form Files**: Retrieves files associated with forms.
- **Submissions**: Accesses submission data with pagination support.
- **Form Properties**: Retrieves properties of forms.

# Error Handling

- **Retry Logic**: Configurable retry logic for handling transient errors.
- **HTTP Status Codes**: Handles common HTTP error codes such as 429, 500, 502, 503, and 504 with retries.
- **Backoff Strategy**: Implements exponential backoff for retries.

# Schema

Each resource has a defined schema with fields such as `id`, `updated_at`, `form_id`, and `created_at`. The schemas are designed to be flexible and accommodate various data types, ensuring compatibility with the API's response structure.