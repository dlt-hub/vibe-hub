# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various resources, each with its own endpoint and schema. The API uses OAuth for authentication and supports pagination through an offset strategy.

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
            "name": "pages",
            "endpoint": {
                "path": "/v1/resource",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                },
                "error_handler": {
                    "retry_on_status_codes": [420, 429],
                    "max_retries": 3,
                    "backoff_factor": 62
                }
            }
        },
        {
            "name": "subscribers",
            "endpoint": {
                "path": "/v1/resource/subscribers",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                },
                "error_handler": {
                    "retry_on_status_codes": [420, 429],
                    "max_retries": 3,
                    "backoff_factor": 62
                }
            }
        }
        // Additional resources can be added here following the same pattern
    ]
})
```

# Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: Use `dlt.secrets["api_key"]` to securely store and access your API key.
- **Location**: The API key is included in the request headers.

# Resources

### Pages
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET
- **Pagination Strategy**: Offset with a limit of 100 records per request.
- **Data Selector**: `data`
- **Primary Key**: `id`

### Subscribers
- **Endpoint Path**: `/v1/resource/subscribers`
- **HTTP Method**: GET
- **Pagination Strategy**: Offset with a limit of 100 records per request.
- **Data Selector**: `data`
- **Primary Key**: `id`

# Error Handling

- **Retry Logic**: The API will retry requests on HTTP status codes 420 and 429.
- **Backoff Strategy**: A constant backoff time of 62 seconds is applied between retries.
- **Max Retries**: 3 attempts will be made before failing the request.

# Schema

### Pages
- **Fields**:
  - `activity_score`: Number, float
  - `allow_email_subscribers`: Boolean or null
  - `created_at`: String, date-time
  - `id`: String, primary key
  - `name`: String
  - `updated_at`: String, date-time
  - Additional fields are available and can be customized as needed.

### Subscribers
- **Fields**:
  - `id`: String, primary key
  - `email`: String
  - `created_at`: String, date-time
  - `mode`: String
  - Additional fields are available and can be customized as needed.

This configuration provides a robust framework for integrating with a third-party analytics API, supporting multiple resources and handling errors gracefully.