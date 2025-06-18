# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration allows for data synchronization from the API, supporting various resources and pagination strategies.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v1",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["api_token"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "people_data",
            "endpoint": {
                "path": "/search/people",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "start_from_page": 1
                }
            }
        },
        {
            "name": "field_data",
            "endpoint": {
                "path": "/fields/person",
                "data_selector": "data",
                "paginator": {
                    "type": "cursor",
                    "cursor_path": "links.next",
                    "stop_condition": "not links.next"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer
- **Token**: Retrieved from `dlt.secrets["api_token"]`

# Resources

## People Data
- **Endpoint Path**: `/search/people`
- **HTTP Method**: GET
- **Pagination Strategy**: Page Number
  - **Page Parameter**: `page`
  - **Start From Page**: 1
- **Data Selector**: `data`

## Field Data
- **Endpoint Path**: `/fields/person`
- **HTTP Method**: GET
- **Pagination Strategy**: Cursor
  - **Cursor Path**: `links.next`
  - **Stop Condition**: `not links.next`
- **Data Selector**: `data`

# Error Handling

- **Retry Logic**: Implement retry on specific HTTP status codes with exponential backoff.
- **Known HTTP Response Codes**: 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

## People Data Schema
- **ID**: string (required)
- **Anniversary Date**: string or null
- **Background URL**: string or null
- **Bio**: string or null
- **Company Name**: string or null
- **Custom Picture URL**: string or null
- **Department**: string or null
- **Direct Report Count**: number or null
- **Directory ID**: string or null
- **Display Name**: string or null
- **Education**: array or null
  - **Class Year**: string or null
  - **Concentrations**: array or null
  - **Degree Type**: string or null
  - **Fields of Study**: array or null
  - **Minors**: array or null
  - **School**: object or null
    - **ID**: string or null
    - **Name**: string or null
- **Email**: string or null
- **Experience**: array or null
  - **Location**: string or null
  - **Role**: string or null
  - **Tenure**: object or null
    - **End**: object or null
      - **Month**: number or null
      - **Year**: number or null
    - **Start**: object or null
      - **Month**: number or null
      - **Year**: number or null
  - **Workplace**: object or null
    - **Logo URL**: string or null
    - **Title**: string or null
- **Extension**: string or null
- **First Name**: string or null
- **Has Name Pronunciation**: boolean or null
- **HRBP Name**: string or null
- **Inclusive Pronouns**: object or null
  - **Code**: string or null
  - **Is Other**: boolean or null
- **Interests**: array or null
- **Is Team Leader**: boolean or null
- **Languages**: array or null
  - **Description**: string or null
  - **Language**: string or null
  - **Proficiency**: object or null
    - **Level**: string or null
    - **Skills**: array or null
- **Last Name**: string or null
- **Message Address**: string or null
- **Office City**: string or null
- **Office State**: string or null
- **Office Street**: string or null
- **Phone**: string or null
- **Picture URL**: string or null
- **Reach Out To Me For**: object or null
  - **General**: array or null
  - **Projects**: array or null
- **Reporting Path**: array or null
- **Skills**: array or null
  - **Is Featured**: boolean or null
  - **Level**: number or null
  - **Name**: string or null
- **Team**: string or null
- **Team Leader ID**: string or null
- **Timezone and Work Hours**: object or null
  - **Timezone**: string or null
  - **Work Hours**: array or null
    - **Day**: number or null
    - **End Time**: object or null
      - **Hour**: number or null
      - **Minute**: number or null
    - **Location**: string or null
    - **Start Time**: object or null
      - **Hour**: number or null
      - **Minute**: number or null
- **Title**: string or null
- **Total Report Count**: number or null

## Field Data Schema
- **ID**: string (required)
- **Type**: string or null
- **Display Name**: string or null
- **Filterable**: boolean or null
- **Object Key**: string or null
- **Searchable**: boolean or null

This configuration provides a clean, vendor-neutral setup for integrating with a third-party analytics API using dltHub's REST API source.