# Overview

This document provides a configuration example for integrating with a third-party event management API using dltHub's REST API source. The integration allows for seamless data synchronization of event-related information such as attendee details, lectures, tracks, and more. This setup supports real-time or scheduled data flow, enabling centralized data analysis for improved event insights and reporting.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/public/v1",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["api_token"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "halls",
            "endpoint": {
                "path": "/content",
                "data_selector": "halls"
            }
        },
        {
            "name": "days",
            "endpoint": {
                "path": "/content",
                "data_selector": "days"
            }
        },
        {
            "name": "lectures",
            "endpoint": {
                "path": "/content",
                "data_selector": "lectures"
            }
        },
        {
            "name": "speakers",
            "endpoint": {
                "path": "/content",
                "data_selector": "speakers"
            }
        },
        {
            "name": "workshops",
            "endpoint": {
                "path": "/content",
                "data_selector": "workshops"
            }
        },
        {
            "name": "pauses",
            "endpoint": {
                "path": "/content",
                "data_selector": "pauses"
            }
        },
        {
            "name": "tracks",
            "endpoint": {
                "path": "/content",
                "data_selector": "tracks"
            }
        },
        {
            "name": "partners",
            "endpoint": {
                "path": "/partners",
                "data_selector": "data"
            }
        },
        {
            "name": "participants",
            "endpoint": {
                "path": "/participants",
                "data_selector": "data"
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer
- **Token**: Use the `api_token` stored in dlt secrets for authentication.

# Resources

- **Resource Names**: halls, days, lectures, speakers, workshops, pauses, tracks, partners, participants
- **Endpoint Paths**: 
  - `/content` for most resources
  - `/partners` for partners
  - `/participants` for participants
- **HTTP Method**: GET (default)
- **Data Selector**: Specific to each resource (e.g., "halls", "days")

# Error Handling

- **Retry Logic**: Implement retry logic for handling transient errors.
- **HTTP Status Codes**: Handle common HTTP errors such as 429, 500, 502, 503, and 504 with retries and backoff strategies.

# Schema

- **Fields**: Each resource has its own schema with fields such as `id`, `name`, `created_at`, etc.
- **Types**: Fields can be of types like string, number, boolean, or null.
- **Primary Keys**: Defined for each resource (e.g., `id` for most resources, `email` for participants).

This configuration provides a clean and generalized setup for integrating with a third-party event management API using dltHub's REST API source, ensuring a vendor-neutral approach.