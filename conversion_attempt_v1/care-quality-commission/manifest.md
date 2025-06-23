# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration supports data synchronization from various resources, each with specific endpoints and pagination strategies.

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
            "name": "inspection_areas",
            "endpoint": {
                "path": "/v1/inspection-areas",
                "data_selector": "inspectionAreas"
            }
        },
        {
            "name": "locations",
            "endpoint": {
                "path": "/v1/locations",
                "data_selector": "locations",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "perPage",
                    "limit": 1000
                }
            }
        },
        {
            "name": "providers",
            "endpoint": {
                "path": "/v1/providers",
                "data_selector": "providers",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "perPage",
                    "limit": 1000
                }
            }
        },
        {
            "name": "provider_locations",
            "endpoint": {
                "path": "/v1/providers/{{ stream_partition['providerId'] }}/locations",
                "data_selector": "locations"
            }
        },
        {
            "name": "locations_detailed",
            "endpoint": {
                "path": "/v1/locations/{{ stream_partition['locationId'] }}",
                "data_selector": []
            }
        },
        {
            "name": "providers_detailed",
            "endpoint": {
                "path": "/v1/providers/{{ stream_partition['providerId'] }}",
                "data_selector": []
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Configuration**: The API key is injected into the request headers using the key name `Ocp-Apim-Subscription-Key`.

## Resources

### Inspection Areas
- **Endpoint Path**: `/v1/inspection-areas`
- **Data Selector**: `inspectionAreas`
- **Primary Key**: `inspectionAreaId`

### Locations
- **Endpoint Path**: `/v1/locations`
- **Data Selector**: `locations`
- **Primary Key**: `locationId`
- **Pagination**: Page number with parameters `page` and `perPage`, limit of 1000.

### Providers
- **Endpoint Path**: `/v1/providers`
- **Data Selector**: `providers`
- **Primary Key**: `providerId`
- **Pagination**: Page number with parameters `page` and `perPage`, limit of 1000.

### Provider Locations
- **Endpoint Path**: `/v1/providers/{{ stream_partition['providerId'] }}/locations`
- **Data Selector**: `locations`
- **Primary Key**: `organisationId`

### Locations Detailed
- **Endpoint Path**: `/v1/locations/{{ stream_partition['locationId'] }}`
- **Data Selector**: `[]`
- **Primary Key**: `locationId`

### Providers Detailed
- **Endpoint Path**: `/v1/providers/{{ stream_partition['providerId'] }}`
- **Data Selector**: `[]`
- **Primary Key**: `providerId`

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504 with a maximum of 3 retries and a backoff factor of 2.

## Schema

Each resource has a defined schema with fields, types, and nullability. Field names are generalized to maintain neutrality, such as `inspectionAreaId`, `locationId`, and `providerId`. The schema ensures that all required fields are present and correctly typed.