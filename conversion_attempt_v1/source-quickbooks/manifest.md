# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports incremental synchronization of various resources, allowing for efficient data retrieval and management.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "oauth2",
            "client_id": dlt.secrets["client_id"],
            "client_secret": dlt.secrets["client_secret"],
            "refresh_token": dlt.secrets["refresh_token"],
            "token_url": "https://oauth.platform.example.com/oauth2/v1/tokens/bearer"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge",
        "endpoint": {
            "params": {
                "limit": 200
            }
        }
    },
    "resources": [
        {
            "name": "resource_name",
            "endpoint": {
                "path": "/v1/resource",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 5
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: OAuth2
- **Secrets or Config Values**:
  - `client_id`
  - `client_secret`
  - `refresh_token`
  - `token_url`: URL for token refresh

## Resources

- **Resource Name**: Abstracted to "resource_name"
- **Endpoint Path and Method**: `/v1/resource`, HTTP method is GET
- **Pagination Strategy**: Offset-based with a limit of 5
- **Data Selector**: `data`

## Error Handling

- **Retry/Backoff Logic**: Not explicitly defined in the YAML, but can be configured as needed.
- **Known HTTP Response Codes**: Not explicitly defined in the YAML, but can be configured as needed.
- **Fallback/Handling Behavior**: Not explicitly defined in the YAML, but can be configured as needed.

## Schema

- **Fields, Types, Nullability**: Defined in the YAML schema section, using neutral field names.
- **Example Fields**:
  - `id`: string
  - `name`: string
  - `active`: boolean
  - `created_at`: date-time
  - `updated_at`: date-time

This configuration provides a clean, vendor-neutral setup for integrating with a third-party analytics API using dltHub's REST API source. It abstracts away any proprietary or branded content, ensuring a generic and reusable integration pattern.