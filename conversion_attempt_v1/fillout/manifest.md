# Overview

This document provides a configuration guide for integrating with a third-party form submission API using dltHub's REST API source. The integration allows for seamless data synchronization of form submissions and related metadata, enabling automated workflows, data analysis, and centralized data management.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v1/api",
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
            "name": "forms",
            "endpoint": {
                "path": "/forms",
                "data_selector": "data"
            }
        },
        {
            "name": "form_metadata",
            "endpoint": {
                "path": "/forms/{form_id}",
                "data_selector": "data"
            }
        },
        {
            "name": "submissions",
            "endpoint": {
                "path": "/forms/{form_id}/submissions",
                "data_selector": "responses",
                "paginator": {
                    "type": "offset",
                    "limit": 150,
                    "offset_param": "offset"
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

### Forms
- **Endpoint Path**: `/forms`
- **Primary Key**: `formId`
- **Data Selector**: `data`

### Form Metadata
- **Endpoint Path**: `/forms/{form_id}`
- **Primary Key**: `id`
- **Data Selector**: `data`

### Submissions
- **Endpoint Path**: `/forms/{form_id}/submissions`
- **Primary Key**: `submissionId`
- **Data Selector**: `responses`
- **Pagination Strategy**: Offset
  - **Limit**: 150
  - **Offset Parameter**: `offset`

## Error Handling

- **Retry Logic**: Implemented for HTTP status codes 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

### Forms
- **Fields**:
  - `formId`: string
  - `id`: number or null
  - `name`: string or null

### Form Metadata
- **Fields**:
  - `id`: string
  - `name`: string or null
  - `calculations`: array or null
  - `documents`: array or null
  - `payments`: array or null
  - `questions`: array or null
  - `scheduling`: array or null
  - `urlParameters`: array or null

### Submissions
- **Fields**:
  - `submissionId`: string
  - `formId`: string
  - `submissionTime`: string
  - `calculations`: array or null
  - `documents`: array or null
  - `lastUpdatedAt`: string or null
  - `payments`: array or null
  - `questions`: array or null
  - `quiz`: object or null
  - `scheduling`: array or null
  - `urlParameters`: array or null

This configuration allows for the extraction and synchronization of form data from a third-party API, supporting automated data workflows and analysis.