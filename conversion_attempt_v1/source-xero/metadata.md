# Overview

This document provides a configuration guide for integrating with a third-party accounting API using dltHub's REST API source. The integration supports both OAuth and Access Token authentication methods, allowing users to choose their preferred method for secure access. The API provides access to various resources, enabling data synchronization and management.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "oauth2",  # or "api_key"
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
            "name": "accounting_data",
            "endpoint": {
                "path": "/v1/accounting",
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

The API supports two authentication methods:

- **OAuth2**: Requires `client_id`, `client_secret`, and `token_url`. This method is suitable for applications that need to authenticate on behalf of a user.
- **API Key**: Requires an `api_key` parameter, which can be included in the query or header, depending on the API's requirements.

# Resources

- **Resource Name**: `accounting_data`
- **Endpoint Path**: `/v1/accounting`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

# Error Handling

The integration includes error handling mechanisms to manage common HTTP errors:

- **Retry Logic**: Automatically retries requests on status codes 429, 500, 502, 503, and 504.
- **Max Retries**: Configured to retry a maximum of 3 times.
- **Backoff Factor**: Uses an exponential backoff strategy with a factor of 2 to manage retry intervals.

# Schema

The schema for the resources is defined with neutral field names to ensure compatibility and ease of use. Fields include identifiers, timestamps, and other relevant data points necessary for synchronization and analysis.