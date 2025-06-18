# Overview

This document provides a configuration guide for integrating with a third-party analytics API using dltHub's REST API source format. The integration allows for seamless data synchronization from the API to various data warehouses, lakes, and destinations. It simplifies data pipelines by extracting data related to clients, projects, expenses, and more, ensuring reliable and efficient data integration for real-time insights.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "bearer",
            "token": dlt.secrets["api_token"]
        }
    },
    "resource_defaults": {
        "primary_key": "id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "clients",
            "endpoint": {
                "path": "/api/workspace/clients",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "startingAfter",
                    "stop_condition": "not response.get('hasMore', False)"
                }
            }
        },
        {
            "name": "projects",
            "endpoint": {
                "path": "/api/workspace/projects",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "startingAfter",
                    "stop_condition": "not response.get('hasMore', False)"
                }
            }
        },
        {
            "name": "project_members",
            "endpoint": {
                "path": "/api/workspace/project-members",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "startingAfter",
                    "stop_condition": "not response.get('hasMore', False)"
                }
            }
        },
        {
            "name": "project_tasks",
            "endpoint": {
                "path": "/api/workspace/project-tasks",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "startingAfter",
                    "stop_condition": "not response.get('hasMore', False)"
                }
            }
        },
        {
            "name": "expense_reports",
            "endpoint": {
                "path": "/api/workspace/expense-reports",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "startingAfter",
                    "stop_condition": "not response.get('hasMore', False)"
                }
            }
        },
        {
            "name": "expense_items",
            "endpoint": {
                "path": "/api/workspace/expense-items",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "startingAfter",
                    "stop_condition": "not response.get('hasMore', False)"
                }
            }
        },
        {
            "name": "expense_categories",
            "endpoint": {
                "path": "/api/workspace/expense-categories",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "startingAfter",
                    "stop_condition": "not response.get('hasMore', False)"
                }
            }
        },
        {
            "name": "project_expense",
            "endpoint": {
                "path": "/api/workspace/project-expenses",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "startingAfter",
                    "stop_condition": "not response.get('hasMore', False)"
                }
            }
        },
        {
            "name": "project_budget_expenses",
            "endpoint": {
                "path": "/api/workspace/project-budget-expenses",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "startingAfter",
                    "stop_condition": "not response.get('hasMore', False)"
                }
            }
        },
        {
            "name": "workspace_members",
            "endpoint": {
                "path": "/api/workspace/members",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "startingAfter",
                    "stop_condition": "not response.get('hasMore', False)"
                }
            }
        },
        {
            "name": "opportunity_stages",
            "endpoint": {
                "path": "/api/workspace/opportunity-stages",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "startingAfter",
                    "stop_condition": "not response.get('hasMore', False)"
                }
            }
        },
        {
            "name": "invoices",
            "endpoint": {
                "path": "/api/workspace/invoices",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "startingAfter",
                    "stop_condition": "not response.get('hasMore', False)"
                }
            }
        },
        {
            "name": "holidays",
            "endpoint": {
                "path": "/api/workspace/holidays",
                "data_selector": "results",
                "paginator": {
                    "type": "cursor",
                    "limit": 100,
                    "cursor_param": "startingAfter",
                    "stop_condition": "not response.get('hasMore', False)"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Bearer
- **Token**: Use the `api_token` stored in `dlt.secrets`.

## Resources

### Clients
- **Endpoint Path**: `/api/workspace/clients`
- **Data Selector**: `results`
- **Pagination**: Cursor-based with `startingAfter` parameter and a limit of 100 records per page.

### Projects
- **Endpoint Path**: `/api/workspace/projects`
- **Data Selector**: `results`
- **Pagination**: Cursor-based with `startingAfter` parameter and a limit of 100 records per page.

### Project Members
- **Endpoint Path**: `/api/workspace/project-members`
- **Data Selector**: `results`
- **Pagination**: Cursor-based with `startingAfter` parameter and a limit of 100 records per page.

### Project Tasks
- **Endpoint Path**: `/api/workspace/project-tasks`
- **Data Selector**: `results`
- **Pagination**: Cursor-based with `startingAfter` parameter and a limit of 100 records per page.

### Expense Reports
- **Endpoint Path**: `/api/workspace/expense-reports`
- **Data Selector**: `results`
- **Pagination**: Cursor-based with `startingAfter` parameter and a limit of 100 records per page.

### Expense Items
- **Endpoint Path**: `/api/workspace/expense-items`
- **Data Selector**: `results`
- **Pagination**: Cursor-based with `startingAfter` parameter and a limit of 100 records per page.

### Expense Categories
- **Endpoint Path**: `/api/workspace/expense-categories`
- **Data Selector**: `results`
- **Pagination**: Cursor-based with `startingAfter` parameter and a limit of 100 records per page.

### Project Expense
- **Endpoint Path**: `/api/workspace/project-expenses`
- **Data Selector**: `results`
- **Pagination**: Cursor-based with `startingAfter` parameter and a limit of 100 records per page.

### Project Budget Expenses
- **Endpoint Path**: `/api/workspace/project-budget-expenses`
- **Data Selector**: `results`
- **Pagination**: Cursor-based with `startingAfter` parameter and a limit of 100 records per page.

### Workspace Members
- **Endpoint Path**: `/api/workspace/members`
- **Data Selector**: `results`
- **Pagination**: Cursor-based with `startingAfter` parameter and a limit of 100 records per page.

### Opportunity Stages
- **Endpoint Path**: `/api/workspace/opportunity-stages`
- **Data Selector**: `results`
- **Pagination**: Cursor-based with `startingAfter` parameter and a limit of 100 records per page.

### Invoices
- **Endpoint Path**: `/api/workspace/invoices`
- **Data Selector**: `results`
- **Pagination**: Cursor-based with `startingAfter` parameter and a limit of 100 records per page.

### Holidays
- **Endpoint Path**: `/api/workspace/holidays`
- **Data Selector**: `results`
- **Pagination**: Cursor-based with `startingAfter` parameter and a limit of 100 records per page.

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3
- **Backoff Factor**: 2

## Schema

Each resource has a schema defined with fields such as `id`, `name`, `createdAt`, and other relevant attributes. The schemas are designed to be flexible, allowing for additional properties and null values where applicable. Use neutral field names to ensure compatibility across different data sources.