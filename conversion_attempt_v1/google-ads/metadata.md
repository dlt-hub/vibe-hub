```markdown
# Overview

This document provides a configuration example for integrating with a third-party analytics API using dltHub's REST API source. The integration supports various data resources and utilizes API key authentication for secure access.

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
            "name": "accounts",
            "endpoint": {
                "path": "/v1/accounts",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 100
                }
            }
        }
        // Additional resources can be added here
    ]
})
```

## Authentication

- **Type**: API Key
- **Secrets**: The API key is stored securely using `dlt.secrets["api_key"]`.

## Resources

- **Resource Name**: Abstracted from the original source
- **Endpoint Path**: Generalized to `/v1/resource`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request
- **Data Selector**: The data is extracted from the `data` field in the response

## Error Handling

- **Retry Logic**: Configured to retry on HTTP status codes 429, 500, 502, 503, and 504
- **Backoff Strategy**: Exponential backoff with a factor of 2
- **Max Retries**: 3 attempts before failing

## Schema

- **Fields**: Use neutral field names such as `customer_id`, `campaign_id`, etc.
- **Types and Nullability**: Defined based on the API's response structure, ensuring compatibility with dltHub's data model

This configuration provides a clean and vendor-neutral setup for integrating with a third-party analytics API using dltHub's REST API source. It abstracts away any proprietary details and focuses on the essential parameters required for a successful integration.
```