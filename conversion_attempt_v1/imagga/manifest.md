# Overview

This document provides a configuration guide for integrating with a third-party analytics API using the dltHub REST API source format. The integration supports various data retrieval operations, including categorization, cropping, color analysis, face detection, text extraction, usage statistics, barcode scanning, and photo categorization. The API requires authentication and supports multiple endpoints with specific data extraction paths.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["api_key"],
            "password": dlt.secrets["api_secret"]
        }
    },
    "resource_defaults": {
        "primary_key": "uuid",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "categorizers",
            "endpoint": {
                "path": "/v2/categorizers",
                "data_selector": "result.categorizers"
            }
        },
        {
            "name": "croppings",
            "endpoint": {
                "path": "/v2/croppings",
                "data_selector": "result.croppings"
            }
        },
        {
            "name": "colors",
            "endpoint": {
                "path": "/v2/colors",
                "data_selector": "result.colors"
            }
        },
        {
            "name": "faces_detections",
            "endpoint": {
                "path": "/v2/faces/detections",
                "data_selector": "result.faces"
            }
        },
        {
            "name": "text",
            "endpoint": {
                "path": "/v2/text",
                "data_selector": "result.text"
            }
        },
        {
            "name": "usage",
            "endpoint": {
                "path": "/v2/usage",
                "data_selector": "result"
            }
        },
        {
            "name": "barcodes",
            "endpoint": {
                "path": "/v2/barcodes",
                "data_selector": "result.barcodes"
            }
        },
        {
            "name": "categorize_photos",
            "endpoint": {
                "path": "/v2/categories/{cat_id}",
                "data_selector": "result.categories",
                "error_handler": {
                    "retry_on_status_codes": [403],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Basic Authentication
- **Secrets**: 
  - `api_key`: Your API key
  - `api_secret`: Your API secret

# Resources

- **Resource Name**: Abstracted from the stream name
- **Endpoint Path**: Defined for each resource
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Not specified, assumed single page
- **Data Selector**: Extracts data from the specified JSON path

# Error Handling

- **Retry Logic**: Configured for specific HTTP status codes
- **Backoff Strategy**: Exponential backoff with a factor of 2
- **Known HTTP Response Codes**: 403 for concurrent request limits

# Schema

Each resource has a defined schema with fields, types, and nullability. Field names are generalized to maintain neutrality. For example, `uuid` is used as a primary key across resources.