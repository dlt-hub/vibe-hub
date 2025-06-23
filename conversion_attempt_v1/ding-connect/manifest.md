# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports multiple resources, each with its own endpoint and pagination strategy. The API requires authentication via an API key, and the data is retrieved in JSON format.

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
        "primary_key": "uuid",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "countries",
            "endpoint": {
                "path": "/v1/get_countries",
                "data_selector": "Items",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "Skip"
                }
            }
        },
        {
            "name": "currencies",
            "endpoint": {
                "path": "/v1/get_currencies",
                "data_selector": "Items",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "Skip"
                }
            }
        },
        {
            "name": "regions",
            "endpoint": {
                "path": "/v1/get_regions",
                "data_selector": "Items",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "Skip"
                }
            }
        },
        {
            "name": "products",
            "endpoint": {
                "path": "/v1/get_products",
                "data_selector": "Items",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "Skip"
                }
            }
        },
        {
            "name": "providers",
            "endpoint": {
                "path": "/v1/get_providers",
                "data_selector": "Items",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "Skip"
                }
            }
        },
        {
            "name": "error_code_descriptions",
            "endpoint": {
                "path": "/v1/get_error_code_descriptions",
                "data_selector": "Items",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "Skip"
                }
            }
        },
        {
            "name": "promotions",
            "endpoint": {
                "path": "/v1/get_promotions",
                "data_selector": "Items",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "Skip"
                }
            }
        },
        {
            "name": "product_descriptions",
            "endpoint": {
                "path": "/v1/get_product_descriptions",
                "data_selector": "Items",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "Skip"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: API Key
- **Configuration**: The API key is injected into the request header for authentication. The key is stored securely using `dlt.secrets`.

# Resources

Each resource corresponds to a specific endpoint and uses an offset-based pagination strategy. The data is extracted from the "Items" field in the JSON response.

- **Resource Name**: Abstracted from the original YAML
- **Endpoint Path**: Generalized to `/v1/resource_name`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Offset with a limit of 100 items per request
- **Data Selector**: "Items"

# Error Handling

The configuration does not specify error handling, but it is recommended to implement retry logic for common HTTP status codes such as 429 (Too Many Requests) and 500-series server errors. A typical configuration might include:

```python
"endpoint": {
    "path": "/v1/resource",
    "data_selector": "data",
    "error_handler": {
        "retry_on_status_codes": [429, 500, 502, 503, 504],
        "max_retries": 3,
        "backoff_factor": 2
    }
}
```

# Schema

The schema for each resource is defined with neutral field names. The primary key for all resources is "uuid", which is a unique identifier added during data transformation. The schema includes various fields with types such as string, number, and boolean, with nullability as specified in the original YAML.