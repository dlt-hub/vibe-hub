# Overview

This document provides a configuration example for integrating with a generic project management and CRM platform's REST API. The integration allows for seamless data synchronization between the platform and various destinations, enabling enhanced data analysis and reporting. The integration supports multiple resources, each with its own endpoint and pagination strategy.

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
            "location": "query"
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "tasks",
            "endpoint": {
                "path": "/v1/task/tasks/list",
                "data_selector": "response.items",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "custom_fields",
            "endpoint": {
                "path": "/v1/customfields/fields/list",
                "data_selector": "response.items",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "agile_workflows",
            "endpoint": {
                "path": "/v1/agile/workflows/list",
                "data_selector": "response.items",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "st_projects_users",
            "endpoint": {
                "path": "/v1/st/project_observers/list",
                "data_selector": "response.items",
                "paginator": {
                    "type": "page_number",
                    "limit": 50,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "projects",
            "endpoint": {
                "path": "/v1/st/projects/list",
                "data_selector": "response.items",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "account",
            "endpoint": {
                "path": "/v1/crm/account/list",
                "data_selector": "response.items",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "agile_epics",
            "endpoint": {
                "path": "/v1/agile/epics/list",
                "data_selector": "response.items",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "loss_reason",
            "endpoint": {
                "path": "/v1/crm/loss_reason/list",
                "data_selector": "response.items",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "pipeline",
            "endpoint": {
                "path": "/v1/crm/pipeline/list",
                "data_selector": "response.items",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "lead",
            "endpoint": {
                "path": "/v1/crm/lead/list",
                "data_selector": "response.items",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "emails",
            "endpoint": {
                "path": "/v1/crm/emails/list",
                "data_selector": "response.items",
                "paginator": {
                    "type": "page_number",
                    "limit": 50,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "invoice",
            "endpoint": {
                "path": "/v1/fin/invoice/list",
                "data_selector": "response.items",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "customer_payment",
            "endpoint": {
                "path": "/v1/fin/customer_payment/list",
                "data_selector": "response.items",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "bank_account",
            "endpoint": {
                "path": "/v1/fin/bank_account/list",
                "data_selector": "response.items",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "agile_stages",
            "endpoint": {
                "path": "/v1/agile/stages/list",
                "data_selector": "response.items",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "agile_sprints",
            "endpoint": {
                "path": "/v1/agile/sprints/list",
                "data_selector": "response.items",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "agile_issues",
            "endpoint": {
                "path": "/v1/agile/issues/list",
                "data_selector": "response.items",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "task_lists",
            "endpoint": {
                "path": "/v1/task/taskslists/list",
                "data_selector": "response.items",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "lists",
            "endpoint": {
                "path": "/v1/task/lists/list",
                "data_selector": "response.items",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "calendar",
            "endpoint": {
                "path": "/v1/calendar/calendar/list",
                "data_selector": "response.items",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "agile_issue_relation_types",
            "endpoint": {
                "path": "/v1/agile/issue_relation_types/list",
                "data_selector": "response.items",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "agile_issue_relation_names",
            "endpoint": {
                "path": "/v1/agile/issue_relation_names/list",
                "data_selector": "response.items",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "agile_issue_type",
            "endpoint": {
                "path": "/v1/agile/issue_type/list",
                "data_selector": "response.items",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "agile_categories",
            "endpoint": {
                "path": "/v1/agile/categories/list",
                "data_selector": "response.items",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "custom_fields_field_sets",
            "endpoint": {
                "path": "/v1/customfields/fieldsets/list",
                "data_selector": "response.items",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "product_list",
            "endpoint": {
                "path": "/v1/products/product/list",
                "data_selector": "response.items",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "product_categories",
            "endpoint": {
                "path": "/v1/products/category/list",
                "data_selector": "response.items",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "product_price_list",
            "endpoint": {
                "path": "/v1/products/pricelist/list",
                "data_selector": "response.items",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "product_manufacturer",
            "endpoint": {
                "path": "/v1/products/manufacturer/list",
                "data_selector": "response.items",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "timesheet",
            "endpoint": {
                "path": "/v1/timetracker/timesheets/list",
                "data_selector": "response.items",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "estimates",
            "endpoint": {
                "path": "/v1/fin/estimate/list",
                "data_selector": "response.items",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "transactions",
            "endpoint": {
                "path": "/v1/fin/transaction/list",
                "data_selector": "response.items",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "invoice_items",
            "endpoint": {
                "path": "/v1/fin/invoice_item/list",
                "data_selector": "response.items",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "invoice_contacts",
            "endpoint": {
                "path": "/v1/fin/invoice_contacts/list",
                "data_selector": "response.items",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "project_observers",
            "endpoint": {
                "path": "/v1/st/project_observers/list",
                "data_selector": "response.items",
                "paginator": {
                    "type": "page_number",
                    "limit": 100,
                    "page_param": "page",
                    "limit_param": "limit"
                }
            }
        },
        {
            "name": "task_workflows",
            "endpoint": {
                "path": "/v1/task/workflows/list",
                "data_selector": "response.items",
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

- **Type**: `api_key`
- **Secrets or Config Values**: Use `api_key` stored in `dlt.secrets["api_key"]`.

# Resources

Each resource is defined with a unique name, endpoint path, and pagination strategy. The data selector is used to extract the relevant data from the API response.

- **Resource Name**: Abstracted from the original stream names.
- **Endpoint Path**: Generalized to `/v1/resource_path`.
- **Pagination Strategy**: Uses `page_number` with parameters `page` and `limit`.

# Error Handling

The configuration does not explicitly define error handling strategies. However, typical error handling in REST API integrations includes retry logic for HTTP status codes like 429 (Too Many Requests) and 500-series server errors, with exponential backoff.

# Schema

The schema for each resource is defined with fields, types, and nullability. Field names are generalized to be vendor-neutral, such as `customer_id` instead of `crm_account_id`. Each schema is consistent with the YAML input and uses neutral field names.