# Overview

This document provides a configuration example for integrating with a third-party analytics API using dltHub's REST API source format. The integration allows for the retrieval of catalog data from the API, utilizing API key authentication and a simple GET request to access the data.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/api/v3",
        "auth": {
            "type": "api_key",
            "name": "Authorization",
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
            "name": "catalogs",
            "endpoint": {
                "path": "/catalog",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: API Key
- **Configuration**: The API key is injected into the request header under the "Authorization" field. The key is stored securely using `dlt.secrets["api_key"]`.

# Resources

- **Resource Name**: catalogs
- **Endpoint Path**: `/catalog`
- **HTTP Method**: GET
- **Pagination Strategy**: Single page (no pagination)
- **Data Selector**: The data is extracted from the "data" field in the response.

# Error Handling

The configuration does not specify error handling logic. It is recommended to implement retry logic for common HTTP status codes such as 429 (Too Many Requests) and 5xx (Server Errors) with exponential backoff.

# Schema

The schema for the "catalogs" resource includes the following fields:

- **type**: string
- **containerType**: string
- **createdAt**: string
- **id**: string (primary key)
- **path**: array
- **tag**: string

All fields are optional, and additional properties are allowed. The schema is defined using JSON Schema Draft-07.