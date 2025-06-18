# Overview

This document provides a configuration guide for integrating with a generic cloud storage API using dltHub's REST API source format. The integration allows for the extraction and synchronization of data from a cloud storage service, supporting various file formats and configurations.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "oauth2",
            "token_url": "https://login.example.com/oauth2/v2.0/token",
            "client_id": dlt.secrets["client_id"],
            "client_secret": dlt.secrets["client_secret"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "generic_resource",
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

- **Type**: OAuth2
- **Token URL**: `https://login.example.com/oauth2/v2.0/token`
- **Secrets**:
  - `client_id`: Your client ID for the API
  - `client_secret`: Your client secret for the API

## Resources

- **Resource Name**: `generic_resource`
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: 
  - Retry on HTTP status codes: 429, 500, 502, 503, 504
  - Maximum retries: 3
  - Backoff factor: 2

## Schema

- **Fields**: 
  - `id`: Primary key for the resource
  - Additional fields should be defined based on the specific data structure of the resource being accessed.

This configuration provides a template for setting up a REST API source in dltHub, abstracting away any vendor-specific details and focusing on the essential parameters required for integration.