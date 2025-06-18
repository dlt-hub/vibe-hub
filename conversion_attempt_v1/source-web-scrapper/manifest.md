# Overview

This document provides a configuration example for integrating with a generic web scraping API using the dltHub REST API source. The integration allows for data synchronization from a web scraping service, providing information about sitemaps, users, scraping jobs, and data quality metrics.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "api_key",
            "name": "api_token",
            "api_key": dlt.secrets["api_token"],
            "location": "query"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "sitemap_list",
            "endpoint": {
                "path": "/api/v1/sitemaps",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 100
                }
            }
        },
        {
            "name": "users",
            "endpoint": {
                "path": "/api/v1/account",
                "data_selector": "data"
            }
        },
        {
            "name": "scraping_jobs",
            "endpoint": {
                "path": "/api/v1/scraping-jobs",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page"
                }
            }
        },
        {
            "name": "scraping_job_data_quality",
            "endpoint": {
                "path": "/api/v1/scraping-job/{{ stream_partition.scraping_job }}/data-quality",
                "data_selector": "data"
            }
        },
        {
            "name": "sitemap_detail",
            "endpoint": {
                "path": "/api/v1/sitemap/{{stream_partition.sitemap_id}}",
                "data_selector": "data"
            }
        }
    ]
})
```

# Authentication

- **Type**: `api_key`
- **Secrets**: Use `dlt.secrets["api_token"]` to securely store and access the API token.
- **Location**: The API token is included as a query parameter in requests.

# Resources

## Sitemap List
- **Endpoint Path**: `/api/v1/sitemaps`
- **Pagination**: Page number-based, with a page size of 100.
- **Data Selector**: `data`

## Users
- **Endpoint Path**: `/api/v1/account`
- **Data Selector**: `data`

## Scraping Jobs
- **Endpoint Path**: `/api/v1/scraping-jobs`
- **Pagination**: Page number-based.
- **Data Selector**: `data`

## Scraping Job Data Quality
- **Endpoint Path**: `/api/v1/scraping-job/{{ stream_partition.scraping_job }}/data-quality`
- **Data Selector**: `data`

## Sitemap Detail
- **Endpoint Path**: `/api/v1/sitemap/{{stream_partition.sitemap_id}}`
- **Data Selector**: `data`

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Backoff Strategy**: Use exponential backoff with a factor of 2 and a maximum of 3 retries.

# Schema

The schema for each resource is defined with neutral field names and types. For example:

- **Sitemap List**: Fields include `id` (number) and `name` (string or null).
- **Users**: Fields include `email`, `firstname`, `lastname` (all string or null), and `page_credits` (number or null).
- **Scraping Jobs**: Fields include `id` (number), `driver`, `status` (string or null), and various job metrics (number or null).
- **Scraping Job Data Quality**: Includes fields for data quality metrics with expected and actual values (number or null) and success status (boolean or null).
- **Sitemap Detail**: Fields include `id` (number), `name`, and `sitemap` (string or null).

This configuration provides a comprehensive setup for integrating with a web scraping API, ensuring data is synchronized efficiently and securely.