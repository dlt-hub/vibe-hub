```markdown
# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source. The integration supports full refresh sync types, allowing you to retrieve data from the API and load it into your data pipeline.

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
- **Secrets or Config Values**: Use `dlt.secrets["api_key"]` to securely store and access your API key.

## Resources

- **Resource Name**: `resource_name`
- **Endpoint Path and Method**: `/v1/resource` (default method: GET)
- **Pagination Strategy**: Offset pagination with a limit of 100 records per request.
- **Data Selector**: `data`

## Error Handling

- **Retry/Backoff Logic**: Implement retry logic with exponential backoff for handling transient errors.
- **Known HTTP Response Codes**: Handle HTTP status codes such as 429 (Too Many Requests) and 500 (Internal Server Error) with retries.
- **Fallback/Handling Behavior**: Ensure robust error handling to manage API rate limits and server errors.

## Schema

- **Fields**: Use neutral field names such as `customer_id`, `event_type`, and `timestamp`.
- **Types and Nullability**: Define field types and nullability based on the API's response schema, ensuring compatibility with your data model.

This configuration guide provides a clean and vendor-neutral approach to integrating with a third-party analytics API using dltHub's REST API source. Follow the outlined steps to set up your data pipeline and ensure seamless data synchronization.
```