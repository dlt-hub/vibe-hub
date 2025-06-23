# Overview

This document provides a vendor-neutral configuration for integrating with a third-party email API using dltHub's REST API source. The integration supports data synchronization through RESTful endpoints, allowing for efficient data extraction and management.

## Configuration Example

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
            "name": "email_data",
            "endpoint": {
                "path": "/v1/emails",
                "data_selector": "emails",
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

- **Type**: `api_key`
- **Configuration**: 
  - `api_key` is stored securely using `dlt.secrets["api_key"]`
  - The API key is included in the query parameters of each request

## Resources

- **Resource Name**: `email_data`
- **Endpoint Path**: `/v1/emails`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request
- **Data Selector**: The data is extracted from the `emails` field in the response

## Error Handling

- **Retry Logic**: 
  - Retries on HTTP status codes 429, 500, 502, 503, and 504
  - Maximum of 3 retries with an exponential backoff factor of 2

## Schema

- **Fields**: 
  - `id`: Primary key for deduplication and merging
  - Other fields are extracted based on the `emails` data structure
- **Field Types**: Generalized to match the API's response structure
- **Nullability**: Fields are nullable unless specified otherwise in the API documentation

This configuration ensures a clean and efficient integration with the email API, abstracting away any vendor-specific details and focusing on the essential patterns for data extraction and synchronization.