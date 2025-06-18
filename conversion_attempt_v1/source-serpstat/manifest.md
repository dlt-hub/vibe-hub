# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various data retrieval operations, including domain history, domain summary, domain keywords, domain keywords by region, domain competitors, and domain top pages. The API uses a POST method for data requests and supports pagination and error handling mechanisms.

# Configuration Example

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
            "location": "header"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "domain_history",
            "endpoint": {
                "path": "/v1/resource",
                "data_selector": "result.data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "X-page",
                    "cursor_path": "response.result.summary_info.page",
                    "stop_condition": "response.result.summary_info.page > config['pages_to_fetch'] - 1"
                }
            }
        },
        {
            "name": "domains_summary",
            "endpoint": {
                "path": "/v1/resource",
                "data_selector": "result.data"
            }
        },
        {
            "name": "domain_keywords",
            "endpoint": {
                "path": "/v1/resource",
                "data_selector": "result.data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "X-page",
                    "cursor_path": "response.result.summary_info.page",
                    "stop_condition": "response.result.summary_info.page > config['pages_to_fetch'] - 1"
                }
            }
        },
        {
            "name": "domain_keywords_by_region",
            "endpoint": {
                "path": "/v1/resource",
                "data_selector": "result.data"
            }
        },
        {
            "name": "domain_competitors",
            "endpoint": {
                "path": "/v1/resource",
                "data_selector": "result.data"
            }
        },
        {
            "name": "domain_top_pages",
            "endpoint": {
                "path": "/v1/resource",
                "data_selector": "result.data",
                "paginator": {
                    "type": "cursor",
                    "cursor_param": "X-page",
                    "cursor_path": "response.result.summary_info.page",
                    "stop_condition": "response.result.summary_info.page > config['pages_to_fetch'] - 1"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: Use `api_key` stored in `dlt.secrets["api_key"]`
- **Location**: Header

# Resources

### Domain History
- **Endpoint Path**: `/v1/resource`
- **Data Selector**: `result.data`
- **Pagination Strategy**: Cursor-based with `X-page` as the cursor parameter

### Domains Summary
- **Endpoint Path**: `/v1/resource`
- **Data Selector**: `result.data`

### Domain Keywords
- **Endpoint Path**: `/v1/resource`
- **Data Selector**: `result.data`
- **Pagination Strategy**: Cursor-based with `X-page` as the cursor parameter

### Domain Keywords by Region
- **Endpoint Path**: `/v1/resource`
- **Data Selector**: `result.data`

### Domain Competitors
- **Endpoint Path**: `/v1/resource`
- **Data Selector**: `result.data`

### Domain Top Pages
- **Endpoint Path**: `/v1/resource`
- **Data Selector**: `result.data`
- **Pagination Strategy**: Cursor-based with `X-page` as the cursor parameter

# Error Handling

- **Retry Logic**: Exponential backoff with a factor of 2
- **Known HTTP Response Codes**: Retry on specific error codes such as 32000
- **Fallback/Handling Behavior**: Retry on rate limit errors, fail on invalid token errors

# Schema

The schema for each resource is defined with neutral field names and types. For example:

```json
{
  "type": "object",
  "$schema": "http://json-schema.org/schema#",
  "properties": {
    "ad_keywords": { "type": "number" },
    "ads": { "type": "number" },
    "date": { "type": "string" },
    "domain": { "type": "string" },
    "down_keywords": { "type": "number" },
    "keywords": { "type": "number" },
    "new_keywords": { "type": "number" },
    "out_keywords": { "type": "number" },
    "rised_keywords": { "type": "number" },
    "traff": { "type": "number" },
    "visible": { "type": "number" },
    "visible_static": { "type": "number" }
  },
  "additionalProperties": true
}
```

This configuration provides a clean, vendor-neutral setup for integrating with a third-party analytics API using dltHub's REST API source.