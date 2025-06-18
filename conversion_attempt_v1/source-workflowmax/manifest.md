# Overview

This document provides a configuration example for integrating with a third-party analytics API using the dltHub REST API source format. The integration supports various resources, each with its own endpoint and data extraction logic. The API uses bearer token authentication and supports pagination and error handling strategies.

# Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["api_key"],
        }
    },
    "resource_defaults": {
        "primary_key": "UUID",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "staff_list",
            "endpoint": {
                "path": "/staff.api/list",
                "data_selector": ["Response", "StaffList", "Staff"],
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "client_list",
            "endpoint": {
                "path": "/client.api/list",
                "data_selector": ["Response", "Clients", "Client"],
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "client_documents",
            "endpoint": {
                "path": "/client.api/documents/{uuid}",
                "data_selector": ["Response", "Documents", "Document"],
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "client_group_list",
            "endpoint": {
                "path": "/clientgroup.api/list",
                "data_selector": ["Response", "Groups", "Group"],
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "cost_list",
            "endpoint": {
                "path": "/cost.api/list",
                "data_selector": ["Response", "Costs", "Cost"],
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "invoice_current",
            "endpoint": {
                "path": "/invoice.api/current",
                "data_selector": ["Response", "Invoices", "Invoice"],
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "invoice_list",
            "endpoint": {
                "path": "/invoice.api/list",
                "data_selector": ["Response", "Invoices", "Invoice"],
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "job_list",
            "endpoint": {
                "path": "/job.api/list",
                "data_selector": ["Response", "Jobs", "Job"],
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "job_tasks",
            "endpoint": {
                "path": "/job.api/tasks",
                "data_selector": ["Response", "Jobs", "Job"],
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "lead_list",
            "endpoint": {
                "path": "/lead.api/list",
                "data_selector": ["Response", "Leads", "Lead"],
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "purchase_order_list",
            "endpoint": {
                "path": "/purchaseorder.api/list",
                "data_selector": ["Response", "PurchaseOrders", "PurchaseOrder"],
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "task_list",
            "endpoint": {
                "path": "/task.api/list",
                "data_selector": ["Response", "TaskList", "Task"],
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "quote_list",
            "endpoint": {
                "path": "/quote.api/list",
                "data_selector": ["Response", "Quotes", "Quote"],
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "supplier_list",
            "endpoint": {
                "path": "/supplier.api/list",
                "data_selector": ["Response", "Suppliers", "Supplier"],
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        },
        {
            "name": "time_list",
            "endpoint": {
                "path": "/time.api/list",
                "data_selector": ["Response", "Times", "Time"],
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit_param": "per_page",
                    "limit": 100
                },
                "error_handler": {
                    "retry_on_status_codes": [429],
                    "max_retries": 3,
                    "backoff_factor": 2
                }
            }
        }
    ]
})
```

# Authentication

- **Type**: Bearer
- **Token**: Retrieved from `dlt.secrets["api_key"]`

# Resources

Each resource is defined with a unique name, endpoint path, data selector, pagination strategy, and error handling configuration. The resources include:

- **staff_list**: `/staff.api/list`
- **client_list**: `/client.api/list`
- **client_documents**: `/client.api/documents/{uuid}`
- **client_group_list**: `/clientgroup.api/list`
- **cost_list**: `/cost.api/list`
- **invoice_current**: `/invoice.api/current`
- **invoice_list**: `/invoice.api/list`
- **job_list**: `/job.api/list`
- **job_tasks**: `/job.api/tasks`
- **lead_list**: `/lead.api/list`
- **purchase_order_list**: `/purchaseorder.api/list`
- **task_list**: `/task.api/list`
- **quote_list**: `/quote.api/list`
- **supplier_list**: `/supplier.api/list`
- **time_list**: `/time.api/list`

# Error Handling

- **Retry on Status Codes**: 429
- **Max Retries**: 3
- **Backoff Factor**: 2

# Schema

Each resource has a schema with fields, types, and nullability. The primary key for most resources is `UUID`. The schemas are defined in a neutral format, ensuring compatibility with various data structures.