# Overview

This document provides a configuration example for integrating with a third-party product information management API using dltHub's REST API source format. The integration allows for seamless data synchronization, enabling efficient management and integration of product catalogs across systems.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com/api/rest/v1",
        "auth": {
            "type": "oauth2",
            "client_id": dlt.secrets["client_id"],
            "client_secret": dlt.secrets["secret"],
            "token_url": "https://api.example.com/api/oauth/v1/token"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "products",
            "endpoint": {
                "path": "/products-uuid",
                "data_selector": "_embedded.items",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 100
                }
            }
        },
        {
            "name": "categories",
            "endpoint": {
                "path": "/categories",
                "data_selector": "_embedded.items",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 100
                }
            }
        },
        {
            "name": "families",
            "endpoint": {
                "path": "/families",
                "data_selector": "_embedded.items",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 100
                }
            }
        },
        {
            "name": "family_variants",
            "endpoint": {
                "path": "/families/{{ stream_partition.family_code }}/variants",
                "data_selector": "_embedded.items",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 100
                }
            }
        },
        {
            "name": "attributes",
            "endpoint": {
                "path": "/attributes",
                "data_selector": "_embedded.items",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 100
                }
            }
        },
        {
            "name": "attribute_groups",
            "endpoint": {
                "path": "/attribute-groups",
                "data_selector": "_embedded.items",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 100
                }
            }
        },
        {
            "name": "association_types",
            "endpoint": {
                "path": "/association-types",
                "data_selector": "_embedded.items",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 100
                }
            }
        },
        {
            "name": "channels",
            "endpoint": {
                "path": "/channels",
                "data_selector": "_embedded.items",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 100
                }
            }
        },
        {
            "name": "locales",
            "endpoint": {
                "path": "/locales",
                "data_selector": "_embedded.items",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 100
                }
            }
        },
        {
            "name": "currencies",
            "endpoint": {
                "path": "/currencies",
                "data_selector": "_embedded.items",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 100
                }
            }
        },
        {
            "name": "measure_families",
            "endpoint": {
                "path": "/measure-families",
                "data_selector": "_embedded.items",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "limit",
                    "limit": 100
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: OAuth2
- **Client ID**: `dlt.secrets["client_id"]`
- **Client Secret**: `dlt.secrets["secret"]`
- **Token URL**: `https://api.example.com/api/oauth/v1/token`

## Resources

### Products
- **Endpoint Path**: `/products-uuid`
- **Data Selector**: `_embedded.items`
- **Pagination**: Page number with `page` and `limit` parameters

### Categories
- **Endpoint Path**: `/categories`
- **Data Selector**: `_embedded.items`
- **Pagination**: Page number with `page` and `limit` parameters

### Families
- **Endpoint Path**: `/families`
- **Data Selector**: `_embedded.items`
- **Pagination**: Page number with `page` and `limit` parameters

### Family Variants
- **Endpoint Path**: `/families/{{ stream_partition.family_code }}/variants`
- **Data Selector**: `_embedded.items`
- **Pagination**: Page number with `page` and `limit` parameters

### Attributes
- **Endpoint Path**: `/attributes`
- **Data Selector**: `_embedded.items`
- **Pagination**: Page number with `page` and `limit` parameters

### Attribute Groups
- **Endpoint Path**: `/attribute-groups`
- **Data Selector**: `_embedded.items`
- **Pagination**: Page number with `page` and `limit` parameters

### Association Types
- **Endpoint Path**: `/association-types`
- **Data Selector**: `_embedded.items`
- **Pagination**: Page number with `page` and `limit` parameters

### Channels
- **Endpoint Path**: `/channels`
- **Data Selector**: `_embedded.items`
- **Pagination**: Page number with `page` and `limit` parameters

### Locales
- **Endpoint Path**: `/locales`
- **Data Selector**: `_embedded.items`
- **Pagination**: Page number with `page` and `limit` parameters

### Currencies
- **Endpoint Path**: `/currencies`
- **Data Selector**: `_embedded.items`
- **Pagination**: Page number with `page` and `limit` parameters

### Measure Families
- **Endpoint Path**: `/measure-families`
- **Data Selector**: `_embedded.items`
- **Pagination**: Page number with `page` and `limit` parameters

## Error Handling

- **Retry on Status Codes**: [429, 500, 502, 503, 504]
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

- **Products**: Includes fields like `uuid`, `categories`, `created`, `enabled`, `family`, `groups`, `parent`, `quantified_associations`, `updated`, `values`, etc.
- **Categories**: Includes fields like `code`, `labels`, `parent`, `updated`, etc.
- **Families**: Includes fields like `code`, `attribute_as_image`, `attribute_as_label`, `attribute_requirements`, `attributes`, `labels`, etc.
- **Family Variants**: Includes fields like `code`, `labels`, `variant_attribute_sets`, etc.
- **Attributes**: Includes fields like `type`, `allowed_extensions`, `auto_option_sorting`, `available_locales`, `code`, `date_min`, `decimals_allowed`, `default_metric_unit`, `group`, `group_labels`, `guidelines`, `is_main_identifier`, `labels`, `localizable`, `metric_family`, `minimum_input_length`, `negative_allowed`, `number_min`, `scopable`, `sort_order`, `unique`, `useable_as_grid_filter`, `validation_regexp`, `validation_rule`, `wysiwyg_enabled`, etc.
- **Attribute Groups**: Includes fields like `code`, `attributes`, `labels`, `sort_order`, etc.
- **Association Types**: Includes fields like `code`, `is_quantified`, `is_two_way`, `labels`, etc.
- **Channels**: Includes fields like `code`, `category_tree`, `conversion_units`, `currencies`, `labels`, `locales`, etc.
- **Locales**: Includes fields like `code`, `enabled`, etc.
- **Currencies**: Includes fields like `code`, `enabled`, `label`, etc.
- **Measure Families**: Includes fields like `code`, `standard`, `units`, etc.