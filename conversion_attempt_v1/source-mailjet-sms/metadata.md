# Overview

This document provides a configuration guide for integrating with a third-party messaging API using dltHub's REST API source format. The integration supports data synchronization from the API, allowing for efficient data extraction and management.

## Configuration Example

Below is an example configuration for setting up the REST API source using dltHub. This configuration is designed to be vendor-neutral and can be adapted to various third-party APIs by adjusting the base URL and authentication details.

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
            "name": "messages",
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

The integration uses API key authentication. The API key should be stored securely and referenced in the configuration as shown in the example. The key is included in the query parameters of each request.

- **Type**: `api_key`
- **Location**: `query`
- **Secret Key**: `api_key`

## Resources

### Messages

- **Resource Name**: messages
- **Endpoint Path**: `/v1/messages`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

## Error Handling

The configuration includes basic error handling to manage common HTTP errors and retry logic.

- **Retry on Status Codes**: 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

The schema for the data extracted from the API should be defined based on the fields available in the API response. Use neutral field names to ensure compatibility across different APIs.

- **Primary Key**: `id`
- **Field Names**: Use generic names like `message_id`, `timestamp`, `content`, etc.

This configuration provides a flexible and reusable template for integrating with various third-party APIs using dltHub's REST API source. Adjust the parameters as needed to fit the specific API you are working with.