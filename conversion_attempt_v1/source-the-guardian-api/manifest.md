# Overview

This document provides a configuration example for integrating with a third-party content API using dltHub's REST API source format. The API allows for querying content based on various parameters such as tags, sections, and date ranges. The integration supports incremental synchronization based on publication dates.

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
            "name": "content",
            "endpoint": {
                "path": "/v1/resource",
                "data_selector": ["response", "results"],
                "paginator": {
                    "type": "page_number",
                    "limit": 10,
                    "page_param": "page",
                    "limit_param": "page_size"
                },
                "error_handler": {
                    "retry_on_status_codes": [429, 500, 502, 503, 504],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            },
            "incremental_sync": {
                "cursor_field": "webPublicationDate",
                "start_datetime": dlt.config["start_date"],
                "end_datetime": dlt.config.get("end_date", "today"),
                "datetime_format": "%Y-%m-%dT%H:%M:%SZ",
                "step": "P7D"
            }
        }
    ]
})
```

# Authentication

- **Type**: `api_key`
- **Secrets**: The API key is stored securely and accessed via `dlt.secrets["api_key"]`.
- **Location**: The API key is included in the query parameters of the request.

# Resources

- **Resource Name**: `content`
- **Endpoint Path**: `/v1/resource`
- **HTTP Method**: `GET`
- **Pagination Strategy**: Page number-based pagination with parameters `page` and `page_size`.
- **Data Selector**: The data is extracted from the `response.results` path.

# Error Handling

- **Retry Logic**: The integration retries on HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 and a maximum of 3 retries.

# Schema

The schema for the `content` resource includes the following fields:

- `id`: string
- `type`: string
- `apiUrl`: string
- `isHosted`: boolean
- `pillarId`: string
- `pillarName`: string
- `sectionId`: string
- `sectionName`: string
- `webPublicationDate`: string
- `webTitle`: string
- `webUrl`: string

All fields are required, and additional properties are allowed. The schema is designed to be flexible and accommodate various content types.