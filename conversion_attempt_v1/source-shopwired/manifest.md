# Overview

This document provides a configuration example for integrating with a generic e-commerce API using the dltHub REST API source format. The integration supports various resources such as countries, pages, products, and more, with capabilities for incremental synchronization and pagination.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.ecommerceapi.uk",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["api_key"],
            "password": dlt.secrets["api_secret"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "countries",
            "endpoint": {
                "path": "/v1/countries",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "pages",
            "endpoint": {
                "path": "/v1/pages",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 20,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "products",
            "endpoint": {
                "path": "/v1/products",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 20,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "products_images",
            "endpoint": {
                "path": "/v1/products/{{ stream_partition['prd_id'] }}/images",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 20,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "choice_sets",
            "endpoint": {
                "path": "/v1/choice-sets",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 20,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "products_reviews",
            "endpoint": {
                "path": "/v1/products/{{ stream_partition['prd_id'] }}/reviews",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 20,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "product_options",
            "endpoint": {
                "path": "/v1/products/{{ stream_partition['prd_id'] }}/options",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 20,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "products_variations",
            "endpoint": {
                "path": "/v1/products/{{ stream_partition['prd_id'] }}/variations",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 20,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "customers",
            "endpoint": {
                "path": "/v1/customers",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 20,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "orders",
            "endpoint": {
                "path": "/v1/orders",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 20,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "shipping_zones",
            "endpoint": {
                "path": "/v1/shipping-zones",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 20,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "events",
            "endpoint": {
                "path": "/v1/events",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 20,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "categories",
            "endpoint": {
                "path": "/v1/categories",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 20,
                    "offset_param": "offset"
                }
            }
        },
        {
            "name": "themes",
            "endpoint": {
                "path": "/v1/themes",
                "data_selector": "data",
                "paginator": {
                    "type": "offset",
                    "limit": 20,
                    "offset_param": "offset"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Basic Authentication
- **Username**: `api_key` (stored in dlt secrets)
- **Password**: `api_secret` (stored in dlt secrets)

## Resources

### Countries
- **Endpoint Path**: `/v1/countries`
- **Pagination**: Single page
- **Primary Key**: `id`

### Pages
- **Endpoint Path**: `/v1/pages`
- **Pagination**: Offset
- **Primary Key**: `id`
- **Incremental Sync**: Based on `created_at_utc`

### Products
- **Endpoint Path**: `/v1/products`
- **Pagination**: Offset
- **Primary Key**: `id`
- **Incremental Sync**: Based on `updated_at_utc`

### Products Images
- **Endpoint Path**: `/v1/products/{{ stream_partition['prd_id'] }}/images`
- **Pagination**: Offset
- **Primary Key**: `id`

### Choice Sets
- **Endpoint Path**: `/v1/choice-sets`
- **Pagination**: Offset
- **Primary Key**: `id`

### Products Reviews
- **Endpoint Path**: `/v1/products/{{ stream_partition['prd_id'] }}/reviews`
- **Pagination**: Offset
- **Primary Key**: `id`
- **Incremental Sync**: Based on `created_at_utc`

### Product Options
- **Endpoint Path**: `/v1/products/{{ stream_partition['prd_id'] }}/options`
- **Pagination**: Offset
- **Primary Key**: `id`

### Products Variations
- **Endpoint Path**: `/v1/products/{{ stream_partition['prd_id'] }}/variations`
- **Pagination**: Offset
- **Primary Key**: `id`

### Customers
- **Endpoint Path**: `/v1/customers`
- **Pagination**: Offset
- **Primary Key**: `id`
- **Incremental Sync**: Based on `created_at_utc`

### Orders
- **Endpoint Path**: `/v1/orders`
- **Pagination**: Offset
- **Primary Key**: `id`
- **Incremental Sync**: Based on `created_at_utc`

### Shipping Zones
- **Endpoint Path**: `/v1/shipping-zones`
- **Pagination**: Offset
- **Primary Key**: `uuid`

### Events
- **Endpoint Path**: `/v1/events`
- **Pagination**: Offset
- **Primary Key**: `id`
- **Incremental Sync**: Based on `created_at_utc`

### Categories
- **Endpoint Path**: `/v1/categories`
- **Pagination**: Offset
- **Primary Key**: `id`
- **Incremental Sync**: Based on `created_at_utc`

### Themes
- **Endpoint Path**: `/v1/themes`
- **Pagination**: Offset
- **Primary Key**: `id`
- **Incremental Sync**: Based on `created_at_utc`

## Error Handling

- **Retry Logic**: Implement retry on HTTP status codes 429, 500, 502, 503, 504
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

Each resource has a defined schema with fields such as `id`, `created_at_utc`, and other relevant attributes. The schemas are designed to be flexible, allowing for additional properties as needed. Field names are generalized to ensure vendor neutrality.