# Overview

This document provides a configuration guide for integrating with a third-party human resources API using dltHub's REST API source format. The integration allows for the extraction of company and collaborator data from the API, supporting data synchronization through a RESTful interface.

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
            "name": "company",
            "endpoint": {
                "path": "/v1/companies/{company_id}",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "collaborators",
            "endpoint": {
                "path": "/v1/companies/{company_id}/collaborators",
                "data_selector": "collaborators",
                "paginator": {
                    "type": "cursor",
                    "limit": 50,
                    "cursor_path": "meta.nextPageToken",
                    "stop_condition": "not meta.nextPageToken"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Bearer Token
- **Token**: Retrieved from `dlt.secrets["api_key"]`

## Resources

### Company

- **Resource Name**: company
- **Endpoint Path**: `/v1/companies/{company_id}`
- **HTTP Method**: GET
- **Pagination Strategy**: single_page
- **Data Selector**: `data`
- **Primary Key**: `id`

### Collaborators

- **Resource Name**: collaborators
- **Endpoint Path**: `/v1/companies/{company_id}/collaborators`
- **HTTP Method**: GET
- **Pagination Strategy**: cursor
  - **Limit**: 50
  - **Cursor Path**: `meta.nextPageToken`
  - **Stop Condition**: `not meta.nextPageToken`
- **Data Selector**: `collaborators`
- **Primary Key**: `id`

## Error Handling

- **Retry Logic**: Implement retry on specific HTTP status codes with exponential backoff.
- **Retry on Status Codes**: [429, 500, 502, 503, 504]
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

### Company

- **Fields**:
  - `id`: string
  - `name`: string or null
  - `country`: string or null
  - `nbActiveContracts`: number or null

### Collaborators

- **Fields**:
  - `id`: string
  - `bic`: string or null
  - `iban`: string or null
  - `emails`: array of objects or null
    - `type`: string or null
    - `email`: string or null
  - `gender`: string or null
  - `lastName`: string or null
  - `teamName`: string or null
  - `addresses`: array of objects or null
    - `type`: string or null
    - `city`: string or null
    - `address`: string or null
    - `country`: string or null
    - `postcode`: string or null
  - `birthDate`: string or null
  - `birthName`: string or null
  - `contracts`: array of objects or null
    - `id`: string or null
    - `status`: string or null
    - `endDate`: string or null
    - `startDate`: string or null
  - `firstName`: string or null
  - `managerId`: string or null
  - `matricule`: string or null
  - `nationality`: string or null
  - `phoneNumbers`: array of objects or null
    - `type`: string or null
    - `phoneNumber`: string or null
  - `countryOfBirth`: string or null
  - `terminationDate`: string or null
  - `socialSecurityNumber`: string or null

This configuration provides a clean and vendor-neutral setup for accessing and synchronizing data from a third-party human resources API using dltHub's REST API source format.