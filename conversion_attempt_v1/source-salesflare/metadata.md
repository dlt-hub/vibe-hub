# Overview

This document provides a configuration guide for integrating with a third-party CRM API using dltHub's REST API source. The integration allows for seamless data extraction from the CRM's endpoints, supporting various data synchronization strategies.

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
- **Configuration**: The API key is stored securely using `dlt.secrets["api_key"]` and is included in the query parameters of each request.

## Resources

- **Resource Name**: A generic name representing the data resource.
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the "data" field in the API response.

## Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors, with a focus on HTTP status codes such as 429 (Too Many Requests) and 5xx server errors.
- **Backoff Strategy**: Exponential backoff is applied with a configurable factor to manage retries effectively.

## Schema

- **Fields**: The schema includes fields with neutral names such as `customer_id`, `resource_name`, and `timestamp`.
- **Types and Nullability**: Each field's data type and nullability are defined based on the API's response structure, ensuring compatibility with the data pipeline.

This configuration ensures a clean and efficient integration with the CRM API, abstracting away any vendor-specific details and focusing on the essential parameters required for data extraction and synchronization.