# Overview

This document provides a configuration example for integrating with a generic file-based API using dltHub's REST API source format. The integration supports various file formats such as CSV, JSON, Excel, Feather, and Parquet. The configuration is designed to be vendor-neutral and does not include any proprietary or branded content.

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
            "name": "file_resource",
            "endpoint": {
                "path": "/v1/files",
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
- **Secrets or Config Values**: Use neutral keys such as `api_key` stored in `dlt.secrets`.

# Resources

- **Resource Name**: `file_resource`
- **Endpoint Path and Method**: `/v1/files`, default method is GET
- **Pagination Strategy**: Offset pagination with a limit of 100
- **Data Selector**: `data`

# Error Handling

- **Retry/Backoff Logic**: Implement retry logic with exponential backoff for HTTP status codes 429, 500, 502, 503, and 504.
- **Known HTTP Response Codes**: Handle standard HTTP error codes with appropriate retry mechanisms.

# Schema

- **Fields**: Use neutral field names such as `file_id`, `file_name`, `file_type`.
- **Types and Nullability**: Define fields with appropriate data types and nullability based on the file structure.

This configuration provides a clean and generalized setup for integrating with a file-based API using dltHub's REST API source format. It abstracts away any vendor-specific details, ensuring a neutral and reusable integration pattern.