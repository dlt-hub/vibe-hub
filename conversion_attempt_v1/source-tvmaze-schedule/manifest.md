# Overview

This document provides a configuration example for integrating with a third-party API that provides TV schedule data. The integration supports retrieving schedules for different regions and timeframes using a REST API. The configuration is designed to be vendor-neutral and follows the dltHub REST API source format.

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
            "name": "domestic_schedule",
            "endpoint": {
                "path": "/v1/schedule",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "web_schedule",
            "endpoint": {
                "path": "/v1/schedule",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "future_schedule",
            "endpoint": {
                "path": "/v1/schedule/full",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: Use `api_key` stored in `dlt.secrets`.

## Resources

### Domestic Schedule

- **Resource Name**: `domestic_schedule`
- **Endpoint Path**: `/v1/schedule`
- **HTTP Method**: `GET`
- **Pagination Strategy**: `single_page`
- **Data Selector**: `data`

### Web Schedule

- **Resource Name**: `web_schedule`
- **Endpoint Path**: `/v1/schedule`
- **HTTP Method**: `GET`
- **Pagination Strategy**: `single_page`
- **Data Selector**: `data`

### Future Schedule

- **Resource Name**: `future_schedule`
- **Endpoint Path**: `/v1/schedule/full`
- **HTTP Method**: `GET`
- **Pagination Strategy**: `single_page`
- **Data Selector**: `data`

## Error Handling

- **Retry/Backoff Logic**: Not specified in the YAML.
- **Known HTTP Response Codes**: Not specified in the YAML.
- **Fallback/Handling Behavior**: Not specified in the YAML.

## Schema

The schema for each resource includes fields such as `id`, `airdate`, `name`, `runtime`, and others. These fields are used to structure the data retrieved from the API. The schema is designed to be flexible and can accommodate null values for most fields.

- **Fields**: 
  - `id`: number
  - `airdate`: string
  - `name`: string
  - `runtime`: number
  - `summary`: string
  - Additional fields are included as per the JSON schema provided in the YAML.

This configuration is designed to be adaptable to various use cases involving TV schedule data retrieval, ensuring a clean and efficient integration with the API.