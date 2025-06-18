# Overview

This document provides a vendor-neutral configuration for integrating with a third-party analytics API using dltHub's REST API source. The integration supports various synchronization types, including full refresh and incremental updates, allowing for flexible data retrieval from the API.

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
            "token_url": "https://api.example.com/oauth/token"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "resource_name",
            "endpoint": {
                "path": "/v1/resource",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: OAuth2
- **Secrets or Config Values**:
  - `client_id`: Your client ID for the API
  - `client_secret`: Your client secret for the API
  - `token_url`: The URL to obtain the OAuth token

# Resources

- **Resource Name**: resource_name
- **Endpoint Path and Method**: `/v1/resource`, default method is GET
- **Pagination Strategy**: Offset pagination with a limit of 100 records per request
- **Data Selector**: `data`

# Error Handling

- **Retry/Backoff Logic**: Implement retry logic with exponential backoff for handling transient errors.
- **Known HTTP Response Codes**: Handle HTTP status codes such as 429 (Too Many Requests), 500 (Internal Server Error), 502 (Bad Gateway), 503 (Service Unavailable), and 504 (Gateway Timeout) with retries.
- **Fallback/Handling Behavior**: Implement a maximum retry limit to prevent infinite retry loops.

# Schema

- **Fields**: Use neutral field names such as `customer_id`, `event_type`, and `timestamp`.
- **Types and Nullability**: Define field types and nullability based on the API's response schema, ensuring compatibility with dltHub's data processing.

This configuration provides a clean and generalized approach to integrating with a third-party analytics API, ensuring compatibility with dltHub's REST API source while abstracting away any proprietary or branded content.