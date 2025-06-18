# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source. The integration supports various resources, each with its own endpoint and configuration settings. The API allows for data retrieval with pagination and supports incremental synchronization based on datetime fields.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["access_key"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge",
        "endpoint": {
            "params": {
                "display": "full",
                "limit": 50
            },
            "headers": {
                "Output-Format": "JSON"
            }
        }
    },
    "resources": [
        {
            "name": "addresses",
            "endpoint": {
                "path": "/addresses",
                "data_selector": "addresses",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "carriers",
            "endpoint": {
                "path": "/carriers",
                "data_selector": "carriers",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "cart_rules",
            "endpoint": {
                "path": "/cart_rules",
                "data_selector": "cart_rules",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "carts",
            "endpoint": {
                "path": "/carts",
                "data_selector": "carts",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "categories",
            "endpoint": {
                "path": "/categories",
                "data_selector": "categories",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "combinations",
            "endpoint": {
                "path": "/combinations",
                "data_selector": "combinations",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "configurations",
            "endpoint": {
                "path": "/configurations",
                "data_selector": "configurations",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "contacts",
            "endpoint": {
                "path": "/contacts",
                "data_selector": "contacts",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "content_management_system",
            "endpoint": {
                "path": "/content_management_system",
                "data_selector": "content_management_system",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "countries",
            "endpoint": {
                "path": "/countries",
                "data_selector": "content_management_system",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "currencies",
            "endpoint": {
                "path": "/currencies",
                "data_selector": "currencies",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "customer_messages",
            "endpoint": {
                "path": "/customer_messages",
                "data_selector": "customer_messages",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "customers",
            "endpoint": {
                "path": "/customers",
                "data_selector": "customers",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "customer_threads",
            "endpoint": {
                "path": "/customer_threads",
                "data_selector": "customer_threads",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "deliveries",
            "endpoint": {
                "path": "/deliveries",
                "data_selector": "deliveries",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "employees",
            "endpoint": {
                "path": "/employees",
                "data_selector": "employees",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "groups",
            "endpoint": {
                "path": "/groups",
                "data_selector": "groups",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "guests",
            "endpoint": {
                "path": "/guests",
                "data_selector": "guests",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "image_types",
            "endpoint": {
                "path": "/image_types",
                "data_selector": "image_types",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "languages",
            "endpoint": {
                "path": "/languages",
                "data_selector": "languages",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "manufacturers",
            "endpoint": {
                "path": "/manufacturers",
                "data_selector": "manufacturers",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "messages",
            "endpoint": {
                "path": "/messages",
                "data_selector": "messages",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "order_carriers",
            "endpoint": {
                "path": "/order_carriers",
                "data_selector": "order_carriers",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "order_details",
            "endpoint": {
                "path": "/order_details",
                "data_selector": "order_details",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "order_histories",
            "endpoint": {
                "path": "/order_histories",
                "data_selector": "order_histories",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "order_invoices",
            "endpoint": {
                "path": "/order_invoices",
                "data_selector": "order_invoices",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "order_payments",
            "endpoint": {
                "path": "/order_payments",
                "data_selector": "order_payments",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "orders",
            "endpoint": {
                "path": "/orders",
                "data_selector": "orders",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "order_slip",
            "endpoint": {
                "path": "/order_slip",
                "data_selector": "order_slips",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "order_states",
            "endpoint": {
                "path": "/order_states",
                "data_selector": "order_states",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "price_ranges",
            "endpoint": {
                "path": "/price_ranges",
                "data_selector": "price_ranges",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "product_customization_fields",
            "endpoint": {
                "path": "/product_customization_fields",
                "data_selector": "customization_fields",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "product_features",
            "endpoint": {
                "path": "/product_features",
                "data_selector": "product_features",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "product_feature_values",
            "endpoint": {
                "path": "/product_feature_values",
                "data_selector": "product_feature_values",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "product_options",
            "endpoint": {
                "path": "/product_options",
                "data_selector": "product_options",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "product_option_values",
            "endpoint": {
                "path": "/product_option_values",
                "data_selector": "product_option_values",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "products",
            "endpoint": {
                "path": "/products",
                "data_selector": "products",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "product_suppliers",
            "endpoint": {
                "path": "/product_suppliers",
                "data_selector": "product_suppliers",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "shop_groups",
            "endpoint": {
                "path": "/shop_groups",
                "data_selector": "shop_groups",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "shops",
            "endpoint": {
                "path": "/shops",
                "data_selector": "shops",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "shop_urls",
            "endpoint": {
                "path": "/shop_urls",
                "data_selector": "shop_urls",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "specific_price_rules",
            "endpoint": {
                "path": "/specific_price_rules",
                "data_selector": "specific_price_rules",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "specific_prices",
            "endpoint": {
                "path": "/specific_prices",
                "data_selector": "specific_prices",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "states",
            "endpoint": {
                "path": "/states",
                "data_selector": "states",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "stock_availables",
            "endpoint": {
                "path": "/stock_availables",
                "data_selector": "stock_availables",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "stock_movement_reasons",
            "endpoint": {
                "path": "/stock_movement_reasons",
                "data_selector": "stock_movement_reasons",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "stock_movements",
            "endpoint": {
                "path": "/stock_movements",
                "data_selector": "stock_mvts",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "stores",
            "endpoint": {
                "path": "/stores",
                "data_selector": "stores",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "suppliers",
            "endpoint": {
                "path": "/suppliers",
                "data_selector": "suppliers",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "tags",
            "endpoint": {
                "path": "/tags",
                "data_selector": "tags",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "taxes",
            "endpoint": {
                "path": "/taxes",
                "data_selector": "taxes",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "tax_rule_groups",
            "endpoint": {
                "path": "/tax_rule_groups",
                "data_selector": "tax_rule_groups",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "tax_rules",
            "endpoint": {
                "path": "/tax_rules",
                "data_selector": "tax_rules",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "translated_configurations",
            "endpoint": {
                "path": "/translated_configurations",
                "data_selector": "translated_configurations",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "weight_ranges",
            "endpoint": {
                "path": "/weight_ranges",
                "data_selector": "weight_ranges",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        },
        {
            "name": "zones",
            "endpoint": {
                "path": "/zones",
                "data_selector": "zones",
                "paginator": {
                    "type": "offset",
                    "limit": 50
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Basic Authentication
- **Secrets**: 
  - `access_key`: The username for basic authentication.

# Resources

Each resource is configured with the following parameters:

- **Resource Name**: The name of the resource.
- **Endpoint Path**: The path to the resource endpoint.
- **Data Selector**: The field path to extract data from the response.
- **Pagination Strategy**: Offset-based pagination with a limit of 50 records per request.

# Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3 retries with exponential backoff.
- **Backoff Factor**: 2

# Schema

The schema for each resource is defined with fields, types, and nullability. Field names are neutral and consistent with the YAML configuration.