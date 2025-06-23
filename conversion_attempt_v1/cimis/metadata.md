# Overview

This document provides a vendor-neutral configuration for integrating with a third-party environmental data API. The integration is designed to fetch data from a specified API endpoint using a RESTful approach. The configuration supports data synchronization through a structured API call pattern.

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
            "name": "environmental_data",
            "endpoint": {
                "path": "/v1/environmental_data",
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

- **Type**: API Key
- **Configuration**: The API key is stored securely and accessed via `dlt.secrets["api_key"]`. The key is included in the query parameters of each request.

# Resources

- **Resource Name**: environmental_data
- **Endpoint Path**: `/v1/environmental_data`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the "data" field in the API response.

# Error Handling

- **Retry Logic**: The configuration includes retry logic for handling transient errors. The system will retry requests on receiving HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff is applied with a factor of 2, and a maximum of 3 retries.

# Schema

- **Fields**: The schema includes fields such as `id`, `timestamp`, `value`, and other relevant data points.
- **Types**: Each field is assigned a type, such as integer, string, or float, based on the data structure.
- **Nullability**: Fields are marked as nullable or non-nullable according to the API's data specifications.

This configuration provides a clean and generalized setup for accessing environmental data through a REST API, ensuring compatibility with dltHub's REST API source format.