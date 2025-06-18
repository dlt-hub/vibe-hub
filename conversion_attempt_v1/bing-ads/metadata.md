# Overview

This document provides a configuration example for integrating with a third-party advertising API using dltHub's REST API source. The integration supports data synchronization from various resources provided by the API, utilizing an API key for authentication.

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
            "name": "campaigns",
            "endpoint": {
                "path": "/v1/campaigns",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        },
        {
            "name": "ad_performance_report_daily",
            "endpoint": {
                "path": "/v1/ad_performance_report_daily",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        }
        // Additional resources can be added here following the same structure
    ]
})
```

## Authentication

- **Type**: API Key
- **Configuration**: The API key is stored securely using `dlt.secrets` and is included in the query parameters of each request.

## Resources

- **Resource Name**: Abstracted from the original source, e.g., "campaigns", "ad_performance_report_daily".
- **Endpoint Path**: Generalized to `/v1/resource_name`.
- **Pagination Strategy**: Uses offset pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the "data" field in the API response.

## Error Handling

- **Retry Logic**: Configured to retry on HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2, up to a maximum of 3 retries.

## Schema

- **Fields**: The schema includes fields such as `id`, `name`, `performance_metrics`, etc., with types and nullability defined based on the API's response structure.
- **Field Names**: Use neutral and descriptive names, avoiding any proprietary identifiers.

This configuration provides a clean and generalized setup for integrating with a third-party advertising API using dltHub, ensuring a vendor-neutral approach while maintaining the necessary functionality for data synchronization.