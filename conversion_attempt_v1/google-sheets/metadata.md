# Overview

This document provides a configuration example for integrating with a third-party spreadsheet API using dltHub's REST API source format. The integration allows for data extraction from spreadsheet resources, supporting various authentication methods and pagination strategies.

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
            "token_url": "https://api.example.com/oauth2/token"
        }
    },
    "resource_defaults": {
        "primary_key": "spreadsheet_id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "spreadsheet_data",
            "endpoint": {
                "path": "/v1/spreadsheets",
                "data_selector": "sheets",
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
- **Secrets**: 
  - `client_id`: The client ID for OAuth2 authentication.
  - `client_secret`: The client secret for OAuth2 authentication.
  - `token_url`: The URL to obtain the OAuth2 token.

# Resources

- **Resource Name**: `spreadsheet_data`
- **Endpoint Path**: `/v1/spreadsheets`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: `sheets` (the field path to extract data from the API response)

# Error Handling

- **Retry Logic**: 
  - Retry on HTTP status codes: 429, 500, 502, 503, 504
  - Maximum retries: 3
  - Backoff factor: 2

# Schema

- **Fields**: 
  - `spreadsheet_id`: Unique identifier for each spreadsheet.
  - Use neutral field names to ensure compatibility with various data sources.

This configuration provides a clean, vendor-neutral setup for accessing spreadsheet data via a REST API, using dltHub's source format. It abstracts away any proprietary or branded content, ensuring a generalized approach suitable for open-source publication or smart assistant training.