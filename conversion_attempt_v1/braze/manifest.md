# Overview

This document provides a configuration guide for integrating with a third-party analytics API using the dltHub REST API source format. The integration supports various data resources, each with specific endpoints and configurations for data retrieval and synchronization.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["api_key"]
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
                "path": "/campaigns/list",
                "data_selector": "campaigns",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 2
                }
            }
        },
        {
            "name": "campaigns_analytics",
            "endpoint": {
                "path": "/campaigns/data_series",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "campaigns_details",
            "endpoint": {
                "path": "/campaigns/details",
                "data_selector": [],
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "canvases",
            "endpoint": {
                "path": "/canvas/list",
                "data_selector": "canvases",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 2
                }
            }
        },
        {
            "name": "canvases_analytics",
            "endpoint": {
                "path": "/canvas/data_series",
                "data_selector": "data.stats",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "canvases_details",
            "endpoint": {
                "path": "/canvas/details",
                "data_selector": [],
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "events",
            "endpoint": {
                "path": "/events/list",
                "data_selector": "events",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 2
                }
            }
        },
        {
            "name": "events_analytics",
            "endpoint": {
                "path": "/events/data_series",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "kpi_daily_new_users",
            "endpoint": {
                "path": "/kpi/new_users/data_series",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "kpi_daily_active_users",
            "endpoint": {
                "path": "/kpi/dau/data_series",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "kpi_daily_app_uninstalls",
            "endpoint": {
                "path": "/kpi/uninstalls/data_series",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "cards",
            "endpoint": {
                "path": "/feed/list",
                "data_selector": "cards",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 2
                }
            }
        },
        {
            "name": "cards_analytics",
            "endpoint": {
                "path": "/feed/data_series",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "segments",
            "endpoint": {
                "path": "/segments/list",
                "data_selector": "segments",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 2
                }
            }
        },
        {
            "name": "segments_analytics",
            "endpoint": {
                "path": "/segments/data_series",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "segments_details",
            "endpoint": {
                "path": "/segments/details",
                "data_selector": [],
                "paginator": {
                    "type": "single_page"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Bearer
- **Token**: Retrieved from `dlt.secrets["api_key"]`

## Resources

### Campaigns
- **Endpoint Path**: `/campaigns/list`
- **Data Selector**: `campaigns`
- **Pagination**: Page number with `page` parameter, limit 2

### Campaigns Analytics
- **Endpoint Path**: `/campaigns/data_series`
- **Data Selector**: `data`
- **Pagination**: Single page

### Campaigns Details
- **Endpoint Path**: `/campaigns/details`
- **Data Selector**: None
- **Pagination**: Single page

### Canvases
- **Endpoint Path**: `/canvas/list`
- **Data Selector**: `canvases`
- **Pagination**: Page number with `page` parameter, limit 2

### Canvases Analytics
- **Endpoint Path**: `/canvas/data_series`
- **Data Selector**: `data.stats`
- **Pagination**: Single page

### Canvases Details
- **Endpoint Path**: `/canvas/details`
- **Data Selector**: None
- **Pagination**: Single page

### Events
- **Endpoint Path**: `/events/list`
- **Data Selector**: `events`
- **Pagination**: Page number with `page` parameter, limit 2

### Events Analytics
- **Endpoint Path**: `/events/data_series`
- **Data Selector**: `data`
- **Pagination**: Single page

### KPI Daily New Users
- **Endpoint Path**: `/kpi/new_users/data_series`
- **Data Selector**: `data`
- **Pagination**: Single page

### KPI Daily Active Users
- **Endpoint Path**: `/kpi/dau/data_series`
- **Data Selector**: `data`
- **Pagination**: Single page

### KPI Daily App Uninstalls
- **Endpoint Path**: `/kpi/uninstalls/data_series`
- **Data Selector**: `data`
- **Pagination**: Single page

### Cards
- **Endpoint Path**: `/feed/list`
- **Data Selector**: `cards`
- **Pagination**: Page number with `page` parameter, limit 2

### Cards Analytics
- **Endpoint Path**: `/feed/data_series`
- **Data Selector**: `data`
- **Pagination**: Single page

### Segments
- **Endpoint Path**: `/segments/list`
- **Data Selector**: `segments`
- **Pagination**: Page number with `page` parameter, limit 2

### Segments Analytics
- **Endpoint Path**: `/segments/data_series`
- **Data Selector**: `data`
- **Pagination**: Single page

### Segments Details
- **Endpoint Path**: `/segments/details`
- **Data Selector**: None
- **Pagination**: Single page

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, 504 with a maximum of 3 retries and a backoff factor of 2.

## Schema

The schema for each resource is defined with neutral field names and types, ensuring compatibility with the dltHub format. Each resource's schema is consistent with the fields and types specified in the YAML configuration.