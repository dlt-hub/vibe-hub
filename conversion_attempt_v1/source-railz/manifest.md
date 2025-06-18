# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various data resources, each with its own endpoint and configuration settings. The API uses OAuth2 authentication to access data, and supports both full refresh and incremental sync types.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "oauth2",
            "client_id": dlt.secrets["client_id"],
            "client_secret": dlt.secrets["secret_key"],
            "token_url": "https://auth.example.com/getAccess"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "businesses",
            "endpoint": {
                "path": "/v1/businesses",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "connections",
            "endpoint": {
                "path": "/v1/connections",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "customers",
            "endpoint": {
                "path": "/v1/customers",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        }
        // Additional resources can be added here
    ]
})
```

## Authentication

The API uses OAuth2 authentication. The following secrets are required:

- `client_id`: The client ID for the application.
- `secret_key`: The client secret key for the application.

The token URL for obtaining access tokens is `https://auth.example.com/getAccess`.

## Resources

Each resource represents a data endpoint in the API. The configuration for each resource includes:

- **Resource Name**: A unique name for the resource.
- **Endpoint Path**: The path to the resource endpoint.
- **Data Selector**: The JSON path to extract data from the API response.
- **Pagination Strategy**: The method used to paginate through data. This example uses offset pagination with a limit of 100 records per request.

## Error Handling

The API configuration includes error handling with retry logic for specific HTTP status codes. The following settings are used:

- **Retry on Status Codes**: [429, 500, 502, 503, 504]
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

The schema for each resource is defined using JSON Schema. Fields are described with types and nullability. Example field names include `id`, `businessName`, `serviceName`, and `customerName`. The schema is designed to be neutral and vendor-agnostic.