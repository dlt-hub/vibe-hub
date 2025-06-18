# Overview

This document provides a vendor-neutral configuration for integrating with a third-party API that offers access to various resources such as organizations, media, links, facility addresses, facilities, events, activities, campsites, permits, recreation area addresses, recreation areas, and tours. The integration is designed to be compatible with dltHub's REST API source format, ensuring a clean and generalized approach to data extraction and synchronization.

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
            "name": "organizations",
            "endpoint": {
                "path": "/v1/organizations",
                "data_selector": "RECDATA",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "media",
            "endpoint": {
                "path": "/v1/media",
                "data_selector": "RECDATA",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "links",
            "endpoint": {
                "path": "/v1/links",
                "data_selector": "RECDATA",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "facilityaddresses",
            "endpoint": {
                "path": "/v1/facilityaddresses",
                "data_selector": "RECDATA",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "facilities",
            "endpoint": {
                "path": "/v1/facilities",
                "data_selector": "RECDATA",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "events",
            "endpoint": {
                "path": "/v1/events",
                "data_selector": "RECDATA",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "activities",
            "endpoint": {
                "path": "/v1/activities",
                "data_selector": "RECDATA",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "campsites",
            "endpoint": {
                "path": "/v1/campsites",
                "data_selector": "RECDATA",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "permits",
            "endpoint": {
                "path": "/v1/permits",
                "data_selector": "RECDATA",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "recreationareaaddresses",
            "endpoint": {
                "path": "/v1/recareaaddresses",
                "data_selector": "RECDATA",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "recreationareas",
            "endpoint": {
                "path": "/v1/recareas",
                "data_selector": "RECDATA",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "tours",
            "endpoint": {
                "path": "/v1/tours",
                "data_selector": "RECDATA",
                "paginator": {
                    "type": "offset",
                    "limit": 50,
                    "offset_param": "offset"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: Use `api_key` stored in `dlt.secrets["api_key"]`
- **Location**: Header

## Resources

### Organizations
- **Endpoint Path**: `/v1/organizations`
- **Pagination Strategy**: Offset
- **Data Selector**: `RECDATA`

### Media
- **Endpoint Path**: `/v1/media`
- **Pagination Strategy**: Offset
- **Data Selector**: `RECDATA`

### Links
- **Endpoint Path**: `/v1/links`
- **Pagination Strategy**: Offset
- **Data Selector**: `RECDATA`

### Facility Addresses
- **Endpoint Path**: `/v1/facilityaddresses`
- **Pagination Strategy**: Offset
- **Data Selector**: `RECDATA`

### Facilities
- **Endpoint Path**: `/v1/facilities`
- **Pagination Strategy**: Offset
- **Data Selector**: `RECDATA`

### Events
- **Endpoint Path**: `/v1/events`
- **Pagination Strategy**: Offset
- **Data Selector**: `RECDATA`

### Activities
- **Endpoint Path**: `/v1/activities`
- **Pagination Strategy**: Offset
- **Data Selector**: `RECDATA`

### Campsites
- **Endpoint Path**: `/v1/campsites`
- **Pagination Strategy**: Offset
- **Data Selector**: `RECDATA`

### Permits
- **Endpoint Path**: `/v1/permits`
- **Pagination Strategy**: Offset
- **Data Selector**: `RECDATA`

### Recreation Area Addresses
- **Endpoint Path**: `/v1/recareaaddresses`
- **Pagination Strategy**: Offset
- **Data Selector**: `RECDATA`

### Recreation Areas
- **Endpoint Path**: `/v1/recareas`
- **Pagination Strategy**: Offset
- **Data Selector**: `RECDATA`

### Tours
- **Endpoint Path**: `/v1/tours`
- **Pagination Strategy**: Offset
- **Data Selector**: `RECDATA`

## Error Handling

- **Retry Logic**: Implement retry on status codes [429, 500, 502, 503, 504]
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

The schema for each resource is defined with fields, types, and descriptions. Field names are generalized to ensure neutrality and compatibility with various data sources.