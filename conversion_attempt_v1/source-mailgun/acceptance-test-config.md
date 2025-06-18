# Overview

This document provides a vendor-neutral configuration for integrating with a third-party email service API using dltHub's REST API source format. The integration supports full refresh sync types, allowing for the retrieval of email-related data from the service.

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
            "name": "email_events",
            "endpoint": {
                "path": "/v1/email_events",
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
- **Configuration**: The API key is stored securely using `dlt.secrets["api_key"]` and is included in the query parameters of each request.

# Resources

- **Resource Name**: `email_events`
- **Endpoint Path**: `/v1/email_events`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset-based pagination with a limit of 100 records per request.
- **Data Selector**: The data is extracted from the `data` field in the API response.

# Error Handling

- **Retry Logic**: The configuration includes retry logic for handling transient errors. The system will retry requests on specific HTTP status codes such as 429 (Too Many Requests) and 5xx server errors.
- **Backoff Strategy**: Exponential backoff is applied with a maximum of 3 retries and a backoff factor of 2.

# Schema

- **Fields**: The schema includes fields such as `id`, `timestamp`, `event_type`, and `recipient_email`. These fields are generalized and do not include any proprietary or branded terms.
- **Types and Nullability**: Each field's type and nullability are defined based on the API's response structure, ensuring compatibility with the data model.

This configuration provides a clean and reusable pattern for integrating with a third-party email service API, abstracting away any proprietary details and focusing on the essential elements required for data extraction and synchronization.