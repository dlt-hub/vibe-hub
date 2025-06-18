# Overview

This document provides a vendor-neutral configuration for integrating with a third-party survey API using dltHub's REST API source. The integration supports data synchronization from the API, allowing for efficient data extraction and management.

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
- **Secrets**: 
  - `api_key`: Stored securely in dlt's secrets management system.

# Resources

- **Resource Name**: `survey_data`
- **Endpoint Path**: `/v1/surveys`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

# Error Handling

- **Retry Logic**: 
  - Retries on HTTP status codes 429, 500, 502, 503, and 504.
  - Maximum of 3 retries with an exponential backoff factor of 2.

# Schema

- **Fields**: 
  - `id`: Primary key for deduplication and data integrity.
  - Other fields are extracted based on the API's response structure, using neutral field names where applicable.

This configuration ensures a clean and efficient integration with the survey API, abstracting away any vendor-specific details and focusing on the essential parameters required for data synchronization.