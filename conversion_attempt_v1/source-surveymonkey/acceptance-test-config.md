# Overview

This document provides a vendor-neutral configuration for integrating with a third-party survey API using dltHub's REST API source format. The integration supports both full refresh and incremental data synchronization, allowing for efficient data retrieval and management.

# Configuration Example

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
            "name": "survey_data",
            "endpoint": {
                "path": "/v1/surveys",
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

# Authentication

- **Type**: `api_key`
- **Configuration**: The API key is stored securely using `dlt.secrets["api_key"]` and is included in the query parameters of each request.

# Resources

- **Resource Name**: `survey_data`
- **Endpoint Path**: `/v1/surveys`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

# Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors, with a focus on HTTP status codes such as 429 (Too Many Requests) and 5xx server errors.
- **Backoff Strategy**: Exponential backoff is applied to manage retries, ensuring that the system does not overwhelm the API with requests.

# Schema

- **Fields**: The schema is defined based on the API's response structure, with neutral field names such as `survey_id`, `title`, and `response_count`.
- **Types and Nullability**: Each field's type and nullability are determined by the API's documentation and response data, ensuring accurate data representation.

This configuration provides a clean and efficient way to integrate with a third-party survey API, leveraging dltHub's capabilities for REST API data extraction and management.