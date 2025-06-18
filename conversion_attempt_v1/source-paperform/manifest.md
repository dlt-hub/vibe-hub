# Overview

This document provides a configuration guide for integrating with a third-party form management API using dltHub's REST API source format. The integration allows for seamless data synchronization and transfer from form submissions to your data warehouse or analytics tools. This setup helps streamline workflows by enabling data extraction, transformation, and loading (ETL) to leverage form data for insights and reporting across systems.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/v1",
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
            "name": "forms",
            "endpoint": {
                "path": "/forms",
                "data_selector": "results.forms",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "skip"
                }
            }
        },
        {
            "name": "form_fields",
            "endpoint": {
                "path": "/forms/{{ form_id }}/fields",
                "data_selector": "results.fields"
            }
        },
        {
            "name": "submissions",
            "endpoint": {
                "path": "/forms/{{ form_id }}/submissions",
                "data_selector": "results.submissions",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "skip"
                }
            }
        },
        {
            "name": "partial_submissions",
            "endpoint": {
                "path": "/forms/{{ form_id }}/partial-submissions",
                "data_selector": "results.partial-submissions",
                "paginator": {
                    "type": "offset",
                    "limit": 100,
                    "offset_param": "skip"
                }
            }
        },
        {
            "name": "coupons",
            "endpoint": {
                "path": "/forms/{{ form_id }}/coupons",
                "data_selector": "results.coupons"
            }
        },
        {
            "name": "products",
            "endpoint": {
                "path": "/forms/{{ form_id }}/products",
                "data_selector": "results.products"
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer
- **Token**: Use the `api_key` stored in `dlt.secrets`.

# Resources

## Forms
- **Endpoint Path**: `/forms`
- **Data Selector**: `results.forms`
- **Pagination**: Offset-based with `limit` of 100 and `offset_param` as `skip`

## Form Fields
- **Endpoint Path**: `/forms/{{ form_id }}/fields`
- **Data Selector**: `results.fields`

## Submissions
- **Endpoint Path**: `/forms/{{ form_id }}/submissions`
- **Data Selector**: `results.submissions`
- **Pagination**: Offset-based with `limit` of 100 and `offset_param` as `skip`

## Partial Submissions
- **Endpoint Path**: `/forms/{{ form_id }}/partial-submissions`
- **Data Selector**: `results.partial-submissions`
- **Pagination**: Offset-based with `limit` of 100 and `offset_param` as `skip`

## Coupons
- **Endpoint Path**: `/forms/{{ form_id }}/coupons`
- **Data Selector**: `results.coupons`

## Products
- **Endpoint Path**: `/forms/{{ form_id }}/products`
- **Data Selector**: `results.products`

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes such as 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

The schema for each resource is defined with neutral field names and types. For example, the `forms` resource includes fields like `id`, `title`, `created_at`, and `updated_at`. Each field is defined with its type and nullability, ensuring compatibility with various data processing systems.