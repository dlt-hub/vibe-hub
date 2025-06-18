# Overview

This document provides a vendor-neutral guide for integrating with a third-party spreadsheet API using dltHub's REST API source format. The integration supports both full refresh and incremental data synchronization, allowing for efficient data retrieval and management.

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
            "name": "spreadsheet_data",
            "endpoint": {
                "path": "/v1/spreadsheets",
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
- **Secrets**: 
  - `client_id`: Your OAuth client ID
  - `client_secret`: Your OAuth client secret
- **Token URL**: `https://api.example.com/oauth/token`

# Resources

- **Resource Name**: `spreadsheet_data`
- **Endpoint Path**: `/v1/spreadsheets`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request
- **Data Selector**: `data`

# Error Handling

- **Retry Logic**: 
  - Retry on HTTP status codes: 429, 500, 502, 503, 504
  - Maximum retries: 3
  - Backoff factor: 2

# Schema

- **Fields**: 
  - `id`: Unique identifier for each record
  - `data`: Contains the spreadsheet data
- **Types**: 
  - `id`: String
  - `data`: JSON object
- **Nullability**: 
  - `id`: Non-nullable
  - `data`: Nullable

This guide ensures a clean and efficient integration with the third-party spreadsheet API, following dltHub's REST API source format and terminology.