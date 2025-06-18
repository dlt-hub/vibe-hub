# Overview

This document provides a guide for integrating with a third-party analytics API using dltHub's REST API source. The integration supports various synchronization types, including full refresh and incremental updates, and is designed to handle authentication via API tokens or OAuth.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "api_key",  # or "oauth2"
            "name": "api_key",
            "api_key": dlt.secrets["api_key"],
            "location": "query"  # or "header"
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

## Authentication

The API supports two types of authentication:
- **API Key Authentication**: Requires an API key to be included in the request, either in the query parameters or headers.
- **OAuth2 Authentication**: Utilizes client credentials to obtain an access token for authorization.

### Secrets or Config Values
- `api_key`: The API key for accessing the service.
- `client_id` and `client_secret`: Used for OAuth2 authentication to obtain the access token.

## Resources

- **Resource Name**: A generic name representing the data resource.
- **Endpoint Path**: `/v1/resource` is the generalized path for accessing the resource.
- **HTTP Method**: Default is GET.
- **Pagination Strategy**: Uses an offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The field path to extract data from the API response.

## Error Handling

The integration includes robust error handling mechanisms:
- **Retry/Backoff Logic**: Automatically retries requests on specific HTTP status codes (e.g., 429, 500, 502, 503, 504) with a maximum of 3 retries and an exponential backoff factor of 2.
- **Known HTTP Response Codes**: Handles common HTTP errors and implements fallback strategies to ensure data integrity.

## Schema

The schema is designed to be neutral and adaptable:
- **Fields**: Includes generic field names such as `id`, `name`, and `created_at`.
- **Types**: Supports common data types like string, integer, and datetime.
- **Nullability**: Fields are nullable unless specified otherwise.

This guide provides a clean, vendor-neutral approach to integrating with a third-party analytics API using dltHub's REST API source, ensuring compatibility and ease of use across different environments.