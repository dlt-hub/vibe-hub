# Overview

This document provides a configuration example for integrating with a third-party aviation data API using the dltHub REST API source format. The integration allows for the extraction of various aviation-related resources such as airports, airlines, airplanes, aircraft types, cities, countries, and taxes. The API supports pagination and error handling mechanisms to ensure reliable data retrieval.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "api_key",
            "name": "access_key",
            "api_key": dlt.secrets["access_key"],
            "location": "query"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "airports",
            "endpoint": {
                "path": "/v1/airports",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                },
                "error_handler": {
                    "retry_on_status_codes": [403],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "airlines",
            "endpoint": {
                "path": "/v1/airlines",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                },
                "error_handler": {
                    "retry_on_status_codes": [403],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "airplanes",
            "endpoint": {
                "path": "/v1/airplanes",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                },
                "error_handler": {
                    "retry_on_status_codes": [403],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "aircraft_types",
            "endpoint": {
                "path": "/v1/aircraft_types",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                },
                "error_handler": {
                    "retry_on_status_codes": [403],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "cities",
            "endpoint": {
                "path": "/v1/cities",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                },
                "error_handler": {
                    "retry_on_status_codes": [403],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "countries",
            "endpoint": {
                "path": "/v1/countries",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                },
                "error_handler": {
                    "retry_on_status_codes": [403],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "taxes",
            "endpoint": {
                "path": "/v1/taxes",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                },
                "error_handler": {
                    "retry_on_status_codes": [403],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: `api_key`
- **Secrets**: Use `dlt.secrets["access_key"]` to securely store and access your API key.
- **Location**: The API key is included in the query parameters of each request.

# Resources

Each resource corresponds to a specific endpoint in the API, with the following configurations:

- **Resource Name**: Abstracted from the original stream names.
- **Endpoint Path**: Defined for each resource, e.g., `/v1/airports`.
- **Pagination Strategy**: Uses an offset-based pagination with a limit of 50 records per request.
- **Data Selector**: Extracts data from the `data` field in the API response.

# Error Handling

- **Retry Logic**: Configured to retry on HTTP status code 403, with a maximum of 3 retries and an exponential backoff factor of 2.
- **Known HTTP Response Codes**: The integration is designed to handle specific HTTP response codes gracefully.

# Schema

The schema for each resource is defined with neutral field names and types, ensuring compatibility with the dltHub format. Each resource has a primary key field named `id`, and additional fields are included as per the original schema definitions.