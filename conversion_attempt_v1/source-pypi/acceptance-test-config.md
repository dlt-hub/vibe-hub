# Overview

This document provides a vendor-neutral guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports full data synchronization from the API, utilizing a RESTful approach to access and manage resources.

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

- **Type**: `api_key`
- **Configuration**: 
  - `name`: The name of the API key parameter.
  - `api_key`: Stored securely in `dlt.secrets`.
  - `location`: Specifies where the API key is included in the request, such as in the query parameters.

## Resources

- **Resource Name**: A generic name representing the data resource.
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: Default is `GET`.
- **Pagination Strategy**: 
  - **Type**: `offset`
  - **Limit**: 100 records per request
- **Data Selector**: The field path to extract data from the API response.

## Error Handling

- **Retry Logic**: 
  - Retries on HTTP status codes: 429, 500, 502, 503, 504
  - Maximum retries: 3
  - Backoff factor: 2

## Schema

- **Fields**: 
  - Use neutral field names such as `analytics_id` instead of any branded identifiers.
- **Types and Nullability**: Defined based on the API's response structure, ensuring compatibility with dltHub's data handling.

This guide ensures a clean and generalized approach to integrating with a third-party analytics API, following dltHub's REST API source format and terminology. All parameters and configurations are derived directly from the provided YAML, with no proprietary or branded content included.