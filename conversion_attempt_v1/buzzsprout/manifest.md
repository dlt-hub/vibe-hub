# Overview

This document provides a configuration example for integrating with a third-party podcast API using dltHub's REST API source format. The integration supports retrieving podcast episodes and podcast details through a RESTful API, utilizing API key-based authentication and handling pagination and error responses.

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
            "location": "header"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "episodes",
            "endpoint": {
                "path": "/{podcast_id}/episodes.json",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "podcasts",
            "endpoint": {
                "path": "/podcasts.json",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: API Key
- **Configuration**: The API key is required and should be stored securely using `dlt.secrets["api_key"]`. The key is included in the request headers for authentication.

## Resources

### Episodes

- **Resource Name**: episodes
- **Endpoint Path**: `/{podcast_id}/episodes.json`
- **HTTP Method**: GET
- **Pagination Strategy**: Single page (no pagination)
- **Data Selector**: The data is extracted from the root of the response.

### Podcasts

- **Resource Name**: podcasts
- **Endpoint Path**: `/podcasts.json`
- **HTTP Method**: GET
- **Pagination Strategy**: Single page (no pagination)
- **Data Selector**: The data is extracted from the root of the response.

## Error Handling

- **Retry Logic**: The integration retries requests on HTTP status code 429 (rate limit) with a maximum of 3 retries and an exponential backoff factor of 2.

## Schema

- **Episodes**: The schema is defined to accommodate episode data, with a primary key of `id`.
- **Podcasts**: The schema is defined to accommodate podcast data, with a primary key of `id`.

This configuration provides a clean and generalized setup for integrating with a podcast API using dltHub's REST API source format, ensuring a vendor-neutral approach.