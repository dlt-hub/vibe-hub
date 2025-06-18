# Overview

This document provides a configuration example for integrating with a third-party sales engagement platform's REST API. The integration allows data extraction from two resources: campaigns and prospects. The API supports pagination and requires API key authentication.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/api/v1/",
        "auth": {
            "type": "api_key",
            "name": "X-API-KEY",
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
            "name": "campaigns",
            "endpoint": {
                "path": "/campaigns",
                "data_selector": ["data", "campaigns"],
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "prospects",
            "endpoint": {
                "path": "/prospects",
                "data_selector": [],
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: API Key
- **Header Name**: `X-API-KEY`
- **Secret Key**: `api_key`

# Resources

## Campaigns

- **Endpoint Path**: `/campaigns`
- **HTTP Method**: GET
- **Data Selector**: `["data", "campaigns"]`
- **Pagination**: Single page (no pagination)

## Prospects

- **Endpoint Path**: `/prospects`
- **HTTP Method**: GET
- **Data Selector**: `[]`
- **Pagination**: Page number
  - **Page Parameter**: `page`
  - **Start From Page**: 1

# Error Handling

- **Retry Logic**: Implement retry on common HTTP status codes such as 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Exponential backoff with a factor of 2 and a maximum of 3 retries.

# Schema

## Campaigns

- **Fields**:
  - `created_at`: string or null
  - `id`: number
  - `name`: string or null
  - `owner_id`: number or null
  - `stats`: object or null
    - `total_clicked`: number or null
    - `total_opened`: number or null
    - `total_replied`: number or null
    - `total_sent`: number or null
  - `status`: string or null

## Prospects

- **Fields**:
  - `data`: object or null
    - `prospects`: array or null
      - `city`: string or null
      - `company`: string or null
      - `country`: string or null
      - `created_at`: string or null
      - `custom_fields`: object or null
      - `email`: string or null
      - `first_name`: string or null
      - `id`: number or null
      - `job_title`: string or null
      - `last_name`: string or null
      - `linkedin_url`: string or null
      - `list`: string or null
      - `object`: string or null
      - `owner_id`: number or null
      - `owner_uuid`: string or null
      - `phone`: string or null
      - `prospect_category`: string or null
      - `prospect_uuid`: string or null
      - `state`: string or null
      - `team_id`: number or null
      - `timezone`: string or null
      - `updated_at`: string or null
  - `message`: string or null
  - `status`: string or null

This configuration provides a clean and vendor-neutral setup for integrating with a generic sales engagement platform's API using dltHub's REST API source format.