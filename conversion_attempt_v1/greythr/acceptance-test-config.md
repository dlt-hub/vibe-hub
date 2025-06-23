# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source. The integration supports data synchronization through a RESTful interface, allowing for efficient data extraction and management.

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

- **Type**: API Key
- **Configuration**: The API key is stored securely using `dlt.secrets["api_key"]` and is included in the query parameters of each request.

## Resources

- **Resource Name**: A generic name representing the data resource.
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the "data" field in the API response.

## Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors, with a backoff strategy to manage request rates.
- **HTTP Response Codes**: The system is configured to handle common HTTP status codes such as 429 (Too Many Requests) and 500-series server errors, with a maximum of 3 retries and an exponential backoff factor of 2.

## Schema

- **Fields**: The schema includes fields with neutral names, such as `id`, `name`, and `value`, ensuring compatibility and generalization across different data sources.
- **Types and Nullability**: Each field is defined with its data type and nullability status, ensuring data integrity and consistency.

This configuration provides a clean, vendor-neutral setup for integrating with a third-party analytics API using dltHub's REST API source, ensuring efficient data synchronization and management.