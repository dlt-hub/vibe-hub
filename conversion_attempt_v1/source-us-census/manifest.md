# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration allows for data retrieval from a specified endpoint, utilizing API key authentication. The configuration supports custom query paths and parameters, enabling flexible data extraction.

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
            "name": "census_data",
            "endpoint": {
                "path": "/v1/resource",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [429, 500, 502, 503, 504],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: API Key
- **Configuration**: The API key is injected into the request as a query parameter. The key is stored securely using `dlt.secrets`.

# Resources

- **Resource Name**: census_data
- **Endpoint Path**: Configurable via `query_path` and `query_params`
- **HTTP Method**: GET
- **Pagination Strategy**: Single page (no pagination)
- **Data Selector**: Custom data extraction logic is applied to parse the response.

# Error Handling

- **Retry Logic**: The integration includes retry logic for handling transient errors. It retries on HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff is applied with a factor of 2, and a maximum of 3 retries.
- **Error Messages**: Custom error messages are configured to handle specific API response scenarios.

# Schema

- **Fields**: The schema is defined using a custom schema loader. Field names and types are abstracted to ensure neutrality and flexibility.
- **Nullability**: Fields are configured to handle null values as per the API's response structure.

This configuration provides a robust framework for integrating with a third-party analytics API, ensuring secure authentication, flexible data retrieval, and reliable error handling.