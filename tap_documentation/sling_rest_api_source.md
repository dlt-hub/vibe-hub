# Sling REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the Sling REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.sling.is/v1/`
- This is the root URL for all API requests. All endpoints are relative to this base URL.

### Authentication
- **Type**: `api_key`
- **Location**: `header`
- **Parameter Name**: `Authorization`
- **Format**: `Bearer {token}`
- The API key must be included in the request headers for authentication.

## 2. Available Endpoints

### Endpoint: Users
- **Path**: `/users`
- **HTTP Method**: `GET`
- **Query Parameters**: None required
- **Response Data Structure**: JSON array of user objects
- **Data Extraction**: The data array is located at the root of the response.

### Endpoint: Leave Types
- **Path**: `/leave/types`
- **HTTP Method**: `GET`
- **Query Parameters**: None required
- **Response Data Structure**: JSON array of leave type objects
- **Data Extraction**: The data array is located at the root of the response.

### Endpoint: Leaves
- **Path**: `/reports/leave`
- **HTTP Method**: `GET`
- **Query Parameters**: `dates` (required, format: `YYYY-MM-DD/YYYY-MM-DD`)
- **Response Data Structure**: JSON object with user IDs as keys and leave details as values
- **Data Extraction**: Iterate over the keys and values to extract leave records.

### Endpoint: No Shows
- **Path**: `/reports/noshows`
- **HTTP Method**: `GET`
- **Query Parameters**: `dates` (required, format: `YYYY-MM-DD/YYYY-MM-DD`)
- **Response Data Structure**: JSON object with composite keys and no-show details as values
- **Data Extraction**: Parse the composite keys to extract individual records.

### Endpoint: Shifts
- **Path**: `/reports/timesheets`
- **HTTP Method**: `GET`
- **Query Parameters**: `dates` (required, format: `YYYY-MM-DD/YYYY-MM-DD`)
- **Response Data Structure**: JSON array of timesheet objects
- **Data Extraction**: The data array is located at the root of the response.

## 3. Incremental Data Loading

### Incremental Support
- **Leaves, No Shows, Shifts**: Use the `dates` query parameter to fetch data incrementally.
- **Date Field**: `date` for Leaves and No Shows, `start_datetime` for Shifts
- **Format**: ISO 8601 date format (`YYYY-MM-DD`)
- **Initial Value**: Use the `start_date` from the configuration or state bookmarks.

## 4. Endpoint Dependencies

### Resource Relationships
- **Shifts**: Requires data from `/labor/cost` to enrich shift records with cost details.
- **Mapping**: Use `shift_id` to correlate data between timesheets and labor costs.

## 5. API Behavior & Limits

### Rate Limiting
- **Limit**: 70 requests per minute
- **Recommendation**: Implement a delay of 1 second between requests to avoid hitting the rate limit.

### Special Requirements
- **Custom Headers**: `Authorization` header with the API key
- **Response Format**: JSON
- **Error Handling**: Check for HTTP status codes and handle non-200 responses appropriately.
- **Data Type Specifications**: Ensure correct data types are used when parsing JSON responses.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.sling.is/v1/",
    "auth": {
        "type": "api_key",
        "location": "header",
        "name": "Authorization",
        "format": "Bearer {token}"
    }
}

ENDPOINTS = [
    {
        "name": "users",
        "path": "/users",
        "method": "GET",
        "data_selector": "root",
        "primary_key": "id",
        "pagination": {
            "type": "single_page"
        }
    },
    {
        "name": "leave_types",
        "path": "/leave/types",
        "method": "GET",
        "data_selector": "root",
        "primary_key": "id",
        "pagination": {
            "type": "single_page"
        }
    },
    {
        "name": "leaves",
        "path": "/reports/leave",
        "method": "GET",
        "data_selector": "root",
        "primary_key": "user_id",
        "pagination": {
            "type": "single_page"
        },
        "incremental": {
            "cursor_path": "date",
            "param_name": "dates",
            "format": "iso"
        }
    },
    {
        "name": "no_shows",
        "path": "/reports/noshows",
        "method": "GET",
        "data_selector": "root",
        "primary_key": "user_id",
        "pagination": {
            "type": "single_page"
        },
        "incremental": {
            "cursor_path": "date",
            "param_name": "dates",
            "format": "iso"
        }
    },
    {
        "name": "shifts",
        "path": "/reports/timesheets",
        "method": "GET",
        "data_selector": "root",
        "primary_key": "id",
        "pagination": {
            "type": "single_page"
        },
        "incremental": {
            "cursor_path": "start_datetime",
            "param_name": "dates",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "shifts",
        "depends_on": "labor_costs",
        "param_mapping": {"shift_id": "id"}
    }
]
```

This configuration guide provides all necessary parameters and considerations for integrating with the Sling REST API using a dlt data pipeline. Adjust the configuration as needed based on specific use cases or additional API features.

---
*dlt REST API Source Configuration Guide*