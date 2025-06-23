# Overview

This document provides a configuration example for integrating with a third-party inventory management API using the dltHub REST API source. The integration allows for the extraction of various resources such as bank accounts, attribute sets, accounts, brands, carriers, customers, deals, locations, products, and more. The API supports pagination and requires authentication via API key.

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
            "name": "bank_accounts",
            "endpoint": {
                "path": "/v1/ref/accountBank",
                "data_selector": "BankAccountsList",
                "paginator": {
                    "type": "page_number",
                    "limit": 1000,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "attribute_sets",
            "endpoint": {
                "path": "/v1/ref/attributeset",
                "data_selector": "AttributeSetList",
                "paginator": {
                    "type": "page_number",
                    "limit": 1000,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "accounts",
            "endpoint": {
                "path": "/v1/ref/account",
                "data_selector": "AccountsList",
                "paginator": {
                    "type": "page_number",
                    "limit": 1000,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "brands",
            "endpoint": {
                "path": "/v1/ref/brand",
                "data_selector": "BrandList",
                "paginator": {
                    "type": "page_number",
                    "limit": 1000,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "carriers",
            "endpoint": {
                "path": "/v1/ref/carrier",
                "data_selector": "CarrierList",
                "paginator": {
                    "type": "page_number",
                    "limit": 1000,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "customers",
            "endpoint": {
                "path": "/v1/customer",
                "data_selector": "CustomerList",
                "paginator": {
                    "type": "page_number",
                    "limit": 1000,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "deals",
            "endpoint": {
                "path": "/v1/reference/deals",
                "data_selector": "Deals",
                "paginator": {
                    "type": "page_number",
                    "limit": 1000,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "locations",
            "endpoint": {
                "path": "/v1/ref/location",
                "data_selector": "LocationList",
                "paginator": {
                    "type": "page_number",
                    "limit": 1000,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "products",
            "endpoint": {
                "path": "/v1/product",
                "data_selector": "Products",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "purchase_list",
            "endpoint": {
                "path": "/v1/purchaseList",
                "data_selector": "PurchaseList",
                "paginator": {
                    "type": "page_number",
                    "limit": 1000,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "suppliers",
            "endpoint": {
                "path": "/v1/supplier",
                "data_selector": "SupplierList",
                "paginator": {
                    "type": "page_number",
                    "limit": 1000,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "opportunities",
            "endpoint": {
                "path": "/v1/crm/opportunity",
                "data_selector": "opportunityList",
                "paginator": {
                    "type": "page_number",
                    "limit": 1000,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "sale_list",
            "endpoint": {
                "path": "/v1/saleList",
                "data_selector": "SaleList",
                "paginator": {
                    "type": "page_number",
                    "limit": 1000,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "taxes",
            "endpoint": {
                "path": "/v1/ref/tax",
                "data_selector": "TaxRuleList",
                "paginator": {
                    "type": "page_number",
                    "limit": 1000,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "product_categories",
            "endpoint": {
                "path": "/v1/ref/category",
                "data_selector": "CategoryList",
                "paginator": {
                    "type": "page_number",
                    "limit": 1000,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "product_family",
            "endpoint": {
                "path": "/v1/productFamily",
                "data_selector": "ProductFamilies",
                "paginator": {
                    "type": "page_number",
                    "limit": 1000,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "purchases",
            "endpoint": {
                "path": "/v1/advanced-purchase",
                "data_selector": [],
                "paginator": {
                    "type": "page_number",
                    "limit": 1000,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "leads",
            "endpoint": {
                "path": "/v1/crm/lead",
                "data_selector": "LeadList",
                "paginator": {
                    "type": "page_number",
                    "limit": 1000,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "sales",
            "endpoint": {
                "path": "/v1/sale",
                "data_selector": [],
                "paginator": {
                    "type": "page_number",
                    "limit": 1000,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "product_availability",
            "endpoint": {
                "path": "/v1/ref/productavailability",
                "data_selector": "ProductAvailabilityList",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: API Key
- **Secrets**: 
  - `api_key`: The API key associated with your account.
  - `accountid`: The ID associated with your account.

# Resources

- **Resource Name**: Abstracted from the original names
- **Endpoint Path**: Generalized paths such as `/v1/ref/accountBank`
- **HTTP Method**: GET (default)
- **Pagination Strategy**: Page number with parameters `page` and `limit`
- **Data Selector**: Fields like `BankAccountsList`, `AttributeSetList`, etc.

# Error Handling

- **Retry Logic**: Implement retry on specific HTTP status codes (e.g., 429, 500, 502, 503, 504)
- **Backoff Strategy**: Exponential backoff with a factor of 2
- **Max Retries**: 3 attempts

# Schema

- **Fields**: Use neutral field names such as `id`, `name`, `description`
- **Types**: String, number, boolean, array, object
- **Nullability**: Fields can be nullable as indicated in the schema

This configuration provides a clean, vendor-neutral setup for integrating with a third-party inventory management API using dltHub's REST API source.