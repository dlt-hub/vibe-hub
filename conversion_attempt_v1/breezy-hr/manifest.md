# Overview

This document provides a configuration example for integrating with a third-party applicant tracking system API using dltHub's REST API source format. The integration allows for the extraction and transformation of data related to positions, candidates, and pipelines.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v3/company/{{ config['company_id'] }}",
        "auth": {
            "type": "api_key",
            "name": "api_key",
            "api_key": dlt.secrets["api_key"],
            "location": "header"
        }
    },
    "resource_defaults": {
        "primary_key": "position_id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "positions",
            "endpoint": {
                "path": "/positions",
                "data_selector": "data",
                "error_handler": {
                    "retry_on_status_codes": [429, 500],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "candidates",
            "endpoint": {
                "path": "/position/{{stream_partition.positions}}/candidates",
                "params": {
                    "sort": "updated_date"
                },
                "data_selector": "data",
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 100,
                    "backoff_factor": 5
                }
            }
        },
        {
            "name": "pipelines",
            "endpoint": {
                "path": "/pipelines",
                "data_selector": "data"
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets**: 
  - `api_key`: The API key for authentication.
  - `company_id`: The company identifier.

## Resources

### Positions
- **Endpoint Path**: `/positions`
- **HTTP Method**: GET
- **Data Selector**: `data`
- **Error Handling**: Retries on status codes 429 and 500 with exponential backoff.

### Candidates
- **Endpoint Path**: `/position/{{stream_partition.positions}}/candidates`
- **HTTP Method**: GET
- **Parameters**: 
  - `sort`: `updated_date`
- **Data Selector**: `data`
- **Error Handling**: Retries on status code 429 with constant backoff.

### Pipelines
- **Endpoint Path**: `/pipelines`
- **HTTP Method**: GET
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: 
  - Positions: Retries on 429 and 500 with a maximum of 3 retries and exponential backoff.
  - Candidates: Retries on 429 with a maximum of 100 retries and constant backoff.
- **Known HTTP Response Codes**: 400, 401, 500 are ignored.

## Schema

### Positions
- **Fields**:
  - `type`: string or null
  - `country_id`: string or null
  - `country_name`: string or null
  - `position_id`: number, string, or null
  - `state`: string or null
  - `tags`: array or null

### Candidates
- **Fields**:
  - `_id`: string or null
  - `name`: string or null
  - `email_address`: string or null
  - `stage`: string or null
  - `position_id`: string or null

### Pipelines
- **Fields**:
  - `_id`: string or null
  - `name`: string or null

This configuration provides a clean and generalized setup for integrating with a third-party applicant tracking system API using dltHub's REST API source format.