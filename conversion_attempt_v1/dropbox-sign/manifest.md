# Overview

This document provides a configuration guide for integrating with a third-party document signing API using dltHub's REST API source format. The integration allows for secure online document signing and management through a RESTful interface. The API supports incremental synchronization based on timestamps and paginated data retrieval.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["api_key"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "signature_requests",
            "endpoint": {
                "path": "/v1/signature_request/list",
                "data_selector": "signature_requests",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "page_size",
                    "limit": 20
                }
            }
        },
        {
            "name": "templates",
            "endpoint": {
                "path": "/v1/template/list",
                "data_selector": "templates",
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

# Authentication

- **Type**: Basic Authentication
- **Credentials**: The API key is used as the username in the basic authentication scheme. It should be stored securely in dlt's secrets management.

# Resources

## Signature Requests

- **Endpoint Path**: `/v1/signature_request/list`
- **Primary Key**: `signature_request_id`
- **Pagination Strategy**: Page number-based pagination with parameters `page` and `page_size`.
- **Data Selector**: `signature_requests`

## Templates

- **Endpoint Path**: `/v1/template/list`
- **Primary Key**: `template_id`
- **Pagination Strategy**: Page number-based pagination with parameters `page` and `page_size`.
- **Data Selector**: `templates`

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504 with a maximum of 3 retries and an exponential backoff factor of 2.

# Schema

## Signature Requests

- **Fields**:
  - `signature_request_id`: string
  - `created_at`: number
  - Additional fields include metadata, email addresses, URLs, and status indicators.

## Templates

- **Fields**:
  - `template_id`: string
  - `updated_at`: number
  - Additional fields include metadata, account information, document details, and roles.

This configuration provides a clean and generalized setup for integrating with a document signing API using dltHub's REST API source format, ensuring secure and efficient data synchronization.