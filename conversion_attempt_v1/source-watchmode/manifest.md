# Overview

This document provides a configuration guide for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various data resources, each with its own endpoint and configuration settings. The API allows for data retrieval through multiple endpoints, each supporting pagination and authentication via API key.

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
            "name": "sources",
            "endpoint": {
                "path": "/v1/sources/",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 250
                }
            }
        },
        {
            "name": "regions",
            "endpoint": {
                "path": "/v1/regions/",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 250
                }
            }
        },
        {
            "name": "networks",
            "endpoint": {
                "path": "/v1/networks/",
                "data_selector": "data"
            }
        },
        {
            "name": "genres",
            "endpoint": {
                "path": "/v1/genres/",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 250
                }
            }
        },
        {
            "name": "search",
            "endpoint": {
                "path": "/v1/search/",
                "data_selector": "title_results",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 250
                }
            }
        },
        {
            "name": "autocomplete_search",
            "endpoint": {
                "path": "/v1/autocomplete-search/",
                "data_selector": "results",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 250
                }
            }
        },
        {
            "name": "titles",
            "endpoint": {
                "path": "/v1/list-titles/",
                "data_selector": "titles",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 250
                }
            }
        },
        {
            "name": "releases",
            "endpoint": {
                "path": "/v1/releases/",
                "data_selector": "releases",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 250
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: API Key
- **Configuration**: The API key is required for authentication and should be included in the query parameters of each request. The key is stored securely using `dlt.secrets`.

## Resources

### Sources
- **Endpoint Path**: `/v1/sources/`
- **Primary Key**: `id`
- **Pagination**: Page number with a limit of 250 records per page.

### Regions
- **Endpoint Path**: `/v1/regions/`
- **Primary Key**: `uuid`
- **Pagination**: Page number with a limit of 250 records per page.

### Networks
- **Endpoint Path**: `/v1/networks/`
- **Primary Key**: `id`
- **Pagination**: None

### Genres
- **Endpoint Path**: `/v1/genres/`
- **Primary Key**: `id`
- **Pagination**: Page number with a limit of 250 records per page.

### Search
- **Endpoint Path**: `/v1/search/`
- **Primary Key**: `id`
- **Pagination**: Page number with a limit of 250 records per page.
- **Data Selector**: `title_results`

### Autocomplete Search
- **Endpoint Path**: `/v1/autocomplete-search/`
- **Primary Key**: `id`
- **Pagination**: Page number with a limit of 250 records per page.
- **Data Selector**: `results`

### Titles
- **Endpoint Path**: `/v1/list-titles/`
- **Primary Key**: `id`
- **Pagination**: Page number with a limit of 250 records per page.
- **Data Selector**: `titles`

### Releases
- **Endpoint Path**: `/v1/releases/`
- **Primary Key**: `id`
- **Pagination**: Page number with a limit of 250 records per page.
- **Data Selector**: `releases`

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504 with a maximum of 3 retries and an exponential backoff factor of 2.

## Schema

Each resource has a defined schema with fields such as `id`, `name`, and other relevant attributes. The schemas are flexible to accommodate null values and are designed to be extensible for additional properties.