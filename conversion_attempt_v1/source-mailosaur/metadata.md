# Overview

This document provides a vendor-neutral configuration for integrating with a third-party email testing API using dltHub's REST API source format. The integration supports data synchronization from the API, allowing for efficient data extraction and management.

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
            "name": "email_messages",
            "endpoint": {
                "path": "/v1/messages",
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

- **Resource Name**: `email_messages`
- **Endpoint Path**: `/v1/messages`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors, with a backoff strategy to manage request rates.
- **HTTP Status Codes**: The system is configured to retry on common transient error codes such as 429 (Too Many Requests) and 5xx server errors.
- **Max Retries**: 3 attempts with exponential backoff.

## Schema

- **Fields**: The schema includes fields such as `id`, `subject`, `sender`, and `received_at`, with types and nullability defined based on the API's response structure.
- **Field Names**: Use neutral and descriptive field names to ensure clarity and consistency.

This configuration provides a clean and efficient way to integrate with a third-party email testing API, leveraging dltHub's capabilities for data extraction and synchronization.