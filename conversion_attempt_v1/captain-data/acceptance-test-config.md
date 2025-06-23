```markdown
# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source. The integration supports full refresh sync types, allowing you to retrieve data from the API in a structured and efficient manner.

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
- **Secrets or Config Values**: Use `dlt.secrets["api_key"]` to securely store and access your API key. The key is included in the query parameters of each request.

## Resources

- **Resource Name**: `resource_name`
- **Endpoint Path and Method**: The API endpoint is accessed via the path `/v1/resource` using the GET method.
- **Pagination Strategy**: The integration uses an offset-based pagination strategy with a limit of 100 records per request.
- **Data Selector**: The data returned from the API is accessed through the `data` field.

## Error Handling

- **Retry/Backoff Logic**: The integration includes retry logic for handling transient errors. It retries on HTTP status codes 429, 500, 502, 503, and 504, with a maximum of 3 retries and an exponential backoff factor of 2.
- **Known HTTP Response Codes**: The integration is designed to handle standard HTTP response codes, ensuring robust error management.

## Schema

- **Fields**: The schema includes fields such as `id`, which serves as the primary key. All field names are neutral and generalized to ensure compatibility with various data sources.
- **Types and Nullability**: Field types and nullability are defined based on the API's response structure, ensuring accurate data representation.

This configuration guide provides a clean and vendor-neutral approach to integrating with a third-party analytics API using dltHub's REST API source. By following the outlined structure, you can efficiently retrieve and manage data from the API.
```