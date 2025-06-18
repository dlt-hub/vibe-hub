# Overview

This document provides a configuration example for integrating with a third-party HR management API using the dltHub REST API source. The integration allows for seamless data extraction of employee and HR-related information, facilitating efficient data management and analysis.

## Configuration Example

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
            "name": "employees",
            "endpoint": {
                "path": "/employees",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 30
                }
            }
        },
        {
            "name": "leave_management_policies",
            "endpoint": {
                "path": "/leave-management/policies",
                "data_selector": "data"
            }
        },
        {
            "name": "documents",
            "endpoint": {
                "path": "/documents",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 30
                }
            }
        },
        {
            "name": "positions",
            "endpoint": {
                "path": "/positions",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 30
                }
            }
        },
        {
            "name": "teams",
            "endpoint": {
                "path": "/teams",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 30
                }
            }
        },
        {
            "name": "terminated_employees",
            "endpoint": {
                "path": "/terminated-employees",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 30
                }
            }
        },
        {
            "name": "termination_reasons",
            "endpoint": {
                "path": "/termination-reasons",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 30
                }
            }
        },
        {
            "name": "individual_allowances",
            "endpoint": {
                "path": "/leave-management/reports/individual-allowances",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 30
                }
            }
        },
        {
            "name": "document_categories",
            "endpoint": {
                "path": "/documents/categories",
                "data_selector": "data"
            }
        },
        {
            "name": "offboarding_categories",
            "endpoint": {
                "path": "/offboarding/categories",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 30
                }
            }
        },
        {
            "name": "onboarding_categories",
            "endpoint": {
                "path": "/onboarding/categories",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 30
                }
            }
        },
        {
            "name": "leave_requests",
            "endpoint": {
                "path": "/leave-management/requests",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "page_param": "page",
                    "limit": 30
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets**: Use `dlt.secrets["api_key"]` to securely store and access your API key.
- **Location**: The API key is included in the request header.

## Resources

### Employees
- **Endpoint Path**: `/employees`
- **Pagination**: Page number with a limit of 30 records per page.
- **Data Selector**: `data`

### Leave Management Policies
- **Endpoint Path**: `/leave-management/policies`
- **Data Selector**: `data`

### Documents
- **Endpoint Path**: `/documents`
- **Pagination**: Page number with a limit of 30 records per page.
- **Data Selector**: `data`

### Positions
- **Endpoint Path**: `/positions`
- **Pagination**: Page number with a limit of 30 records per page.
- **Data Selector**: `data`

### Teams
- **Endpoint Path**: `/teams`
- **Pagination**: Page number with a limit of 30 records per page.
- **Data Selector**: `data`

### Terminated Employees
- **Endpoint Path**: `/terminated-employees`
- **Pagination**: Page number with a limit of 30 records per page.
- **Data Selector**: `data`

### Termination Reasons
- **Endpoint Path**: `/termination-reasons`
- **Pagination**: Page number with a limit of 30 records per page.
- **Data Selector**: `data`

### Individual Allowances
- **Endpoint Path**: `/leave-management/reports/individual-allowances`
- **Pagination**: Page number with a limit of 30 records per page.
- **Data Selector**: `data`

### Document Categories
- **Endpoint Path**: `/documents/categories`
- **Data Selector**: `data`

### Offboarding Categories
- **Endpoint Path**: `/offboarding/categories`
- **Pagination**: Page number with a limit of 30 records per page.
- **Data Selector**: `data`

### Onboarding Categories
- **Endpoint Path**: `/onboarding/categories`
- **Pagination**: Page number with a limit of 30 records per page.
- **Data Selector**: `data`

### Leave Requests
- **Endpoint Path**: `/leave-management/requests`
- **Pagination**: Page number with a limit of 30 records per page.
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504.
- **Max Retries**: 3 retries with exponential backoff.
- **Backoff Factor**: 2

## Schema

### Employees
- **Fields**: `email`, `employment_start_date`, `employment_status`, `first_name`, `id`, `irregular_contract_worker`, `last_name`, `personal_identification_number`, `position`, `position_id`, `reports_to_employee_id`, `tax_number`
- **Primary Key**: `id`

### Leave Management Policies
- **Fields**: None specified
- **Primary Key**: `id`

### Documents
- **Fields**: `description`, `acceptance_required`, `company_id`, `created_at`, `created_by`, `document_category_id`, `document_expires`, `file_content_type`, `file_name`, `file_scan_result`, `file_scan_started_at`, `file_size`, `file_updated_at`, `id`, `last_edited_by`, `right_to_work_number`, `shared_with_direct_manager`, `shared_with_everyone`, `shared_with_team_manager`, `source`, `updated_at`
- **Primary Key**: `id`

### Positions
- **Fields**: None specified
- **Primary Key**: `id`

### Teams
- **Fields**: None specified
- **Primary Key**: `id`

### Terminated Employees
- **Fields**: `email`, `employment_start_date`, `employment_status`, `first_name`, `id`, `irregular_contract_worker`, `last_name`, `personal_identification_number`, `position`, `tax_number`, `team`, `team_id`, `termination_date`
- **Primary Key**: `id`

### Termination Reasons
- **Fields**: `type`, `id`, `name`
- **Primary Key**: `id`

### Individual Allowances
- **Fields**: `eligibilities`, `full_name`, `id`
- **Primary Key**: `id`

### Document Categories
- **Fields**: `documents_count`, `id`, `name`
- **Primary Key**: `id`

### Offboarding Categories
- **Fields**: `id`, `title`
- **Primary Key**: `id`

### Onboarding Categories
- **Fields**: `id`, `title`
- **Primary Key**: `id`

### Leave Requests
- **Fields**: `details`, `employee_id`, `end_date`, `fields`, `first_part_of_day`, `hours`, `id`, `is_multi_date`, `is_part_of_day`, `is_single_day`, `policy_id`, `request_date`, `second_part_of_day`, `shared_person_name`, `shared_person_nin`, `specific_time`, `start_date`, `status`, `status_code`
- **Primary Key**: `id`