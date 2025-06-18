```markdown
# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source. The integration supports full data synchronization from the API, utilizing a RESTful approach to access and manage resources.

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

The integration uses API key-based authentication. The API key should be stored securely and accessed via `dlt.secrets["api_key"]`. The key is included in the query parameters of each request.

## Resources

- **Resource Name**: A generic name for the resource being accessed.
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the "data" field in the API response.

## Error Handling

The integration includes basic error handling with retry logic for common HTTP status codes. The configuration allows for up to 3 retries with an exponential backoff factor of 2 for status codes such as 429, 500, 502, 503, and 504.

## Schema

The schema for the data is defined with neutral field names and types. The primary key for the resource is "id", and the data is expected to be structured in a way that supports merging operations during data synchronization.

```

This document provides a clean, vendor-neutral guide for configuring a REST API source using dltHub, abstracting away any proprietary or branded content from the original YAML configuration.