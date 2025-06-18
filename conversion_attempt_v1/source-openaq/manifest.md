# Overview

This document provides a configuration guide for integrating with a third-party API that offers access to global environmental data. The integration allows fetching data from various resources such as instruments, manufacturers, locations, and more. The API supports pagination and error handling to ensure reliable data retrieval.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v3/",
        "auth": {
            "type": "api_key",
            "name": "api_key",
            "api_key": dlt.secrets["api_key"],
            "location": "header"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "instruments",
            "endpoint": {
                "path": "/v1/instruments",
                "data_selector": "results",
                "paginator": {
                    "type": "page_number",
                    "limit": 1000,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "manufacturers",
            "endpoint": {
                "path": "/v1/manufacturers",
                "data_selector": "results",
                "paginator": {
                    "type": "page_number",
                    "limit": 1000,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "locations",
            "endpoint": {
                "path": "/v1/locations",
                "data_selector": "results",
                "paginator": {
                    "type": "page_number",
                    "limit": 1000,
                    "page_param": "page",
                    "limit_param": "limit"
                },
                "error_handler": {
                    "retry_on_status_codes": [500],
                    "max_retries": 2,
                    "backoff_factor": 5
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access your API key.
- **Location**: The API key is included in the request header.

## Resources

### Instruments
- **Endpoint Path**: `/v1/instruments`
- **Pagination**: Page number-based with a limit of 1000 records per page.
- **Data Selector**: `results`

### Manufacturers
- **Endpoint Path**: `/v1/manufacturers`
- **Pagination**: Page number-based with a limit of 1000 records per page.
- **Data Selector**: `results`

### Locations
- **Endpoint Path**: `/v1/locations`
- **Pagination**: Page number-based with a limit of 1000 records per page.
- **Data Selector**: `results`
- **Error Handling**: Retries on HTTP 500 errors with a maximum of 2 retries and a constant backoff of 5 seconds.

## Error Handling

- **Retry Logic**: Configured to retry on specific HTTP status codes (e.g., 500).
- **Backoff Strategy**: Constant backoff with a specified time interval between retries.
- **Max Retries**: Defined per resource to ensure robust data retrieval.

## Schema

The schema for each resource is defined with neutral field names and types. For example, the `instruments` resource includes fields like `id`, `name`, and `manufacturer`, with appropriate data types and nullability.

This configuration ensures a clean and efficient integration with the API, abstracting away any vendor-specific details and focusing on the essential parameters and patterns required for data extraction.