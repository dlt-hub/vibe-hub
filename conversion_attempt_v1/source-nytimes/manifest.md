# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source format. The integration supports retrieving data from various resources, such as archived articles and popular content, using a vendor-neutral approach.

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
            "name": "archive",
            "endpoint": {
                "path": "/v1/archive",
                "data_selector": "response.docs",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "most_popular_emailed",
            "endpoint": {
                "path": "/v1/mostpopular/emailed",
                "data_selector": "results",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "most_popular_shared",
            "endpoint": {
                "path": "/v1/mostpopular/shared",
                "data_selector": "results",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "most_popular_viewed",
            "endpoint": {
                "path": "/v1/mostpopular/viewed",
                "data_selector": "results",
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

### Archive

- **Endpoint Path**: `/v1/archive`
- **HTTP Method**: GET
- **Pagination Strategy**: `single_page`
- **Data Selector**: `response.docs`

### Most Popular Emailed

- **Endpoint Path**: `/v1/mostpopular/emailed`
- **HTTP Method**: GET
- **Pagination Strategy**: `single_page`
- **Data Selector**: `results`

### Most Popular Shared

- **Endpoint Path**: `/v1/mostpopular/shared`
- **HTTP Method**: GET
- **Pagination Strategy**: `single_page`
- **Data Selector**: `results`

### Most Popular Viewed

- **Endpoint Path**: `/v1/mostpopular/viewed`
- **HTTP Method**: GET
- **Pagination Strategy**: `single_page`
- **Data Selector**: `results`

## Error Handling

- **Retry/Backoff Logic**: Not specified in the YAML, default dltHub behavior applies.
- **Known HTTP Response Codes**: Not specified in the YAML, default dltHub behavior applies.
- **Fallback/Handling Behavior**: Not specified in the YAML, default dltHub behavior applies.

## Schema

The schema for each resource is defined with neutral field names and types, ensuring compatibility with the dltHub format. The fields include identifiers, URLs, publication dates, and other metadata relevant to the articles or content being retrieved. Each field is described with its type and nullability, ensuring a clear understanding of the data structure.