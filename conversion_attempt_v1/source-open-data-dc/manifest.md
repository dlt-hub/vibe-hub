# Overview

This document provides a configuration guide for integrating with a third-party open data API. The API allows for the retrieval of data related to addresses, units, and other geographical information. The integration supports data synchronization through RESTful API endpoints, utilizing API key-based authentication.

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
            "name": "locations",
            "endpoint": {
                "path": "/v1/locations/{location}",
                "data_selector": ["Result", "addresses"],
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "units",
            "endpoint": {
                "path": "/v1/units/{marid}",
                "data_selector": ["Result", "units"],
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "ssls",
            "endpoint": {
                "path": "/v1/ssls",
                "data_selector": ["Result", "ssls"],
                "paginator": {
                    "type": "single_page"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: API Key
- **Configuration**: The API key is injected into the request as a query parameter. The key should be stored securely and accessed via `dlt.secrets["api_key"]`.

## Resources

### Locations
- **Endpoint Path**: `/v1/locations/{location}`
- **HTTP Method**: GET
- **Data Selector**: `["Result", "addresses"]`
- **Pagination**: Single page

### Units
- **Endpoint Path**: `/v1/units/{marid}`
- **HTTP Method**: GET
- **Data Selector**: `["Result", "units"]`
- **Pagination**: Single page
- **Primary Key**: `UnitNum`

### SSLs
- **Endpoint Path**: `/v1/ssls`
- **HTTP Method**: GET
- **Data Selector**: `["Result", "ssls"]`
- **Pagination**: Single page
- **Primary Key**: `SSL`

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

### Locations
- **Fields**:
  - `address`: Object containing address details
  - `distance`: Number
  - `units`: String
  - `zones`: Object

### Units
- **Fields**:
  - `FullAddress`: String
  - `MarId`: String (required)
  - `Status`: String
  - `UnitNum`: String
  - `UnitSSL`: String
  - `UnitType`: String

### SSLs
- **Fields**:
  - `Col`: String
  - `FullAddress`: String
  - `Lot`: String
  - `Lot_type`: String
  - `MarId`: String
  - `SSL`: String (required)
  - `Square`: String

This configuration provides a structured approach to accessing and synchronizing data from the specified API, ensuring a seamless integration with dltHub's REST API source framework.