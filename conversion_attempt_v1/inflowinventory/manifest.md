# Overview

This document provides a configuration example for integrating with a third-party inventory management API using the dltHub REST API source. The integration allows for data extraction from various resources such as customers, products, and sales orders.

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
            "name": "adjustment_reasons",
            "endpoint": {
                "path": "/{company_id}/adjustment-reasons",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "categories",
            "endpoint": {
                "path": "/{company_id}/categories",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "currencies",
            "endpoint": {
                "path": "/{company_id}/currencies",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "custom_fields",
            "endpoint": {
                "path": "/{company_id}/custom-fields",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "locations",
            "endpoint": {
                "path": "/{company_id}/locations",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "operation_types",
            "endpoint": {
                "path": "/{company_id}/operation-types",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "payment_terms",
            "endpoint": {
                "path": "/{company_id}/payment-terms",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "pricing_schemes",
            "endpoint": {
                "path": "/{company_id}/pricing-schemes",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "products",
            "endpoint": {
                "path": "/{company_id}/products",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "product_cost_adjustments",
            "endpoint": {
                "path": "/{company_id}/product-cost-adjustments",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "purchase_orders",
            "endpoint": {
                "path": "/{company_id}/purchase-orders",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "sales_orders",
            "endpoint": {
                "path": "/{company_id}/sales-orders",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "stock_adjustments",
            "endpoint": {
                "path": "/{company_id}/stock-adjustments",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "stock_counts",
            "endpoint": {
                "path": "/{company_id}/stock-counts",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "stock_transfers",
            "endpoint": {
                "path": "/{company_id}/stock-transfers",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "tax_codes",
            "endpoint": {
                "path": "/{company_id}/tax-codes",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "taxing_schemes",
            "endpoint": {
                "path": "/{company_id}/taxing-schemes",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "team_members",
            "endpoint": {
                "path": "/{company_id}/team-members",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "vendors",
            "endpoint": {
                "path": "/{company_id}/vendors",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        },
        {
            "name": "customers",
            "endpoint": {
                "path": "/{company_id}/customers",
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

- **Type**: `api_key`
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access the API key.
- **Location**: The API key is included in the request header under the `Authorization` field.

# Resources

Each resource corresponds to a specific endpoint and is configured with the following parameters:

- **Resource Name**: A generalized name for the resource.
- **Endpoint Path**: The path to the resource, parameterized with `{company_id}`.
- **HTTP Method**: `GET` (default).
- **Data Selector**: The field path to extract data, set to `data`.
- **Pagination Strategy**: `single_page` for resources that do not require pagination.

# Error Handling

- **Retry Logic**: Implement retry logic for handling transient errors.
- **HTTP Status Codes**: Handle common HTTP status codes such as 429, 500, 502, 503, and 504 with retries.
- **Backoff Strategy**: Use exponential backoff with a factor of 2 for retries.

# Schema

The schema for each resource is defined with neutral field names and types, ensuring compatibility with the dltHub format. Each resource's primary key is specified to ensure data integrity and uniqueness.