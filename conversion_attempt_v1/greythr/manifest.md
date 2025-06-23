# Overview

This document provides a configuration guide for integrating with a third-party employee management and payroll API. The integration allows for the automated extraction and synchronization of employee data, which can be used for reporting, analytics, or further processing.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "basic",
            "username": dlt.secrets["username"],
            "password": dlt.secrets["password"]
        }
    },
    "resource_defaults": {
        "primary_key": "employee_id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "employees",
            "endpoint": {
                "path": "/employee/v2/employees",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit_param": "size",
                    "page_param": "page",
                    "limit": 100
                }
            }
        },
        {
            "name": "users_list",
            "endpoint": {
                "path": "/user/v2/users",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit_param": "size",
                    "page_param": "page",
                    "limit": 100
                }
            }
        },
        {
            "name": "employees_profile",
            "endpoint": {
                "path": "/employee/v2/employees/profile",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit_param": "size",
                    "page_param": "page",
                    "limit": 100
                }
            }
        },
        {
            "name": "employees_categories",
            "endpoint": {
                "path": "/employee/v2/employees/categories",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit_param": "size",
                    "page_param": "page",
                    "limit": 100
                }
            }
        },
        {
            "name": "employee_bank_details",
            "endpoint": {
                "path": "/employee/v2/employees/bank",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit_param": "size",
                    "page_param": "page",
                    "limit": 100
                }
            }
        },
        {
            "name": "employees_work_details",
            "endpoint": {
                "path": "/employee/v2/employees/work",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit_param": "size",
                    "page_param": "page",
                    "limit": 100
                }
            }
        },
        {
            "name": "employee_pf_esi_details",
            "endpoint": {
                "path": "/employee/v2/employees/pf",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit_param": "size",
                    "page_param": "page",
                    "limit": 100
                }
            }
        },
        {
            "name": "employee_statutory_details",
            "endpoint": {
                "path": "/employee/v2/employees/statutory/india",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit_param": "size",
                    "page_param": "page",
                    "limit": 100
                }
            }
        },
        {
            "name": "employees_personal_details",
            "endpoint": {
                "path": "/employee/v2/employees/personal",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit_param": "size",
                    "page_param": "page",
                    "limit": 100
                }
            }
        },
        {
            "name": "employee_separation_details",
            "endpoint": {
                "path": "/employee/v2/employees/separation",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit_param": "size",
                    "page_param": "page",
                    "limit": 100
                }
            }
        },
        {
            "name": "employee_qualifications_details",
            "endpoint": {
                "path": "/employee/v2/employees/qualifications",
                "data_selector": "data",
                "paginator": {
                    "type": "page_number",
                    "limit_param": "size",
                    "page_param": "page",
                    "limit": 100
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: Basic Authentication
- **Secrets**: 
  - `username`: Your API username
  - `password`: Your API password

## Resources

### Employees
- **Endpoint Path**: `/employee/v2/employees`
- **Pagination**: Page number with `size` and `page` parameters
- **Data Selector**: `data`
- **Primary Key**: `employee_id`

### Users List
- **Endpoint Path**: `/user/v2/users`
- **Pagination**: Page number with `size` and `page` parameters
- **Data Selector**: `data`

### Employees Profile
- **Endpoint Path**: `/employee/v2/employees/profile`
- **Pagination**: Page number with `size` and `page` parameters
- **Data Selector**: `data`
- **Primary Key**: `employee_id`

### Employees Categories
- **Endpoint Path**: `/employee/v2/employees/categories`
- **Pagination**: Page number with `size` and `page` parameters
- **Data Selector**: `data`
- **Primary Key**: `employee_id`

### Employee Bank Details
- **Endpoint Path**: `/employee/v2/employees/bank`
- **Pagination**: Page number with `size` and `page` parameters
- **Data Selector**: `data`
- **Primary Key**: `employee_id`

### Employees Work Details
- **Endpoint Path**: `/employee/v2/employees/work`
- **Pagination**: Page number with `size` and `page` parameters
- **Data Selector**: `data`
- **Primary Key**: `employee_id`

### Employee PF & ESI Details
- **Endpoint Path**: `/employee/v2/employees/pf`
- **Pagination**: Page number with `size` and `page` parameters
- **Data Selector**: `data`
- **Primary Key**: `employee_id`

### Employee Statutory Details
- **Endpoint Path**: `/employee/v2/employees/statutory/india`
- **Pagination**: Page number with `size` and `page` parameters
- **Data Selector**: `data`
- **Primary Key**: `employee_id`

### Employees Personal Details
- **Endpoint Path**: `/employee/v2/employees/personal`
- **Pagination**: Page number with `size` and `page` parameters
- **Data Selector**: `data`
- **Primary Key**: `employee_id`

### Employee Separation Details
- **Endpoint Path**: `/employee/v2/employees/separation`
- **Pagination**: Page number with `size` and `page` parameters
- **Data Selector**: `data`
- **Primary Key**: `employee_id`

### Employee Qualifications Details
- **Endpoint Path**: `/employee/v2/employees/qualifications`
- **Pagination**: Page number with `size` and `page` parameters
- **Data Selector**: `data`

## Error Handling

- **Retry Logic**: Implement retry logic for HTTP status codes 429, 500, 502, 503, and 504 with a maximum of 3 retries and a backoff factor of 2.

## Schema

### Employees
- **Fields**: 
  - `employee_id`: number
  - `name`: string or null
  - `email`: string or null
  - `title`: string or null
  - `gender`: string or null
  - `mobile`: string or null
  - `status`: number or null
  - `leftorg`: boolean or null
  - `lastName`: string or null
  - `firstName`: string or null
  - `dateOfJoin`: string or null
  - `employeeNo`: string or null
  - `dateOfBirth`: string or null
  - `leavingDate`: string or null
  - `lastModified`: string or null
  - `personalEmail`: string or null
  - `probationPeriod`: number or null

### Users List
- **Fields**: 
  - `type`: number or null
  - `id`: number or null
  - `admin`: boolean or null
  - `email`: string or null
  - `deleted`: boolean or null
  - `userName`: string or null
  - `systemPassword`: boolean or null

### Employees Profile
- **Fields**: 
  - `twitter`: string or null
  - `wishDOB`: string or null
  - `facebook`: string or null
  - `linkedIn`: string or null
  - `nickname`: string or null
  - `biography`: string or null
  - `employee_id`: number

### Employees Categories
- **Fields**: 
  - `employee_id`: number
  - `categoryList`: array or null

### Employee Bank Details
- **Fields**: 
  - `bankName`: number or null
  - `bankBranch`: number or null
  - `branchCode`: string or null
  - `employee_id`: number
  - `bankAccountNumber`: string or null
  - `salaryPaymentMode`: number or null

### Employees Work Details
- **Fields**: 
  - `employee_id`: number
  - `confirmDate`: string or null
  - `noticePeriod`: number or null
  - `onboardingStatus`: string or null
  - `extendedProbationDays`: number or null

### Employee PF & ESI Details
- **Fields**: 
  - `uan`: string or null
  - `pfNumber`: string or null
  - `employee_id`: number
  - `pfEligible`: boolean or null
  - `esiEligible`: boolean or null
  - `pfKycLinkedOn`: string or null
  - `pfExistingMember`: boolean or null
  - `excessEPFContribution`: boolean or null
  - `excessEPSContribution`: boolean or null

### Employee Statutory Details
- **Fields**: 
  - `disabled`: boolean or null
  - `birthplace`: string or null
  - `employee_id`: number
  - `expatriate`: boolean or null
  - `fatherName`: string or null
  - `isDirector`: boolean or null
  - `nationality`: number or null
  - `countryOfOrigin`: number or null
  - `residentialStatus`: number or null

### Employees Personal Details
- **Fields**: 
  - `bloodGroup`: number or null
  - `employee_id`: number
  - `spouseName`: string or null
  - `maritalStatus`: number or null

### Employee Separation Details
- **Fields**: 
  - `leftOrg`: boolean or null
  - `employee_id`: number
  - `leavingDate`: string or null
  - `leavingReason`: number or null
  - `fitToBeRehired`: boolean or null
  - `submissionDate`: string or null
  - `exitInterviewDate`: string or null
  - `finalSettlementDate`: string or null
  - `submittedResignation`: boolean or null
  - `tentativeLeavingDate`: string or null

### Employee Qualifications Details
- **Fields**: 
  - `id`: number or null
  - `duration`: string or null
  - `employee`: number or null
  - `qualArea`: number or null
  - `qualYear`: number or null
  - `institute`: string or null
  - `qualLevel`: number or null
  - `qualDescription`: number or null
  - `qualCompletionYear`: number or null

This configuration provides a comprehensive setup for integrating with the API, ensuring data is accurately extracted and synchronized for further use.