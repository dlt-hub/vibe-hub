# Overview

This document provides a configuration example for integrating with a third-party accounting API using dltHub's REST API source. The integration supports data synchronization through RESTful endpoints, allowing for both incremental and full refresh data extraction.

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

- **Resource Name**: Abstracted to "resource_name"
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the "data" field in the API response.

## Error Handling

- **Retry Logic**: The configuration includes retry logic for handling transient errors. The system will retry requests on specific HTTP status codes such as 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff is applied with a factor of 2, and a maximum of 3 retries.

## Schema

- **Fields**: The schema is defined with neutral field names, ensuring compatibility and avoiding proprietary terms.
- **Primary Key**: The primary key for deduplication and data integrity is set to "id".
- **Write Disposition**: The default write disposition is "merge", allowing for incremental updates to existing records.

This configuration provides a clean and generalized setup for integrating with a third-party accounting API, ensuring compatibility with dltHub's REST API source framework.