# Zendesk Sell REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation for Zendesk Sell

This document provides a comprehensive guide for configuring a dlt data pipeline to extract data from the Zendesk Sell REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.getbase.com/v2/`
- **Version Path**: `/v2/`

### Authentication
- **Type**: `bearer`
- **Token Format**: `Bearer {access_token}`
- **Parameter Name**: `Authorization`
- **Location**: Header

## 2. Available Endpoints

### Endpoint: Users
- **Path**: `/users`
- **HTTP Method**: `GET`
- **Required Query Parameters**: None
- **Optional Query Parameters**: `per_page`, `page`, `sort_by`
- **Response Data Structure**: JSON array of user objects

### Pagination Configuration
- **Type**: `page_number`
- **Parameter Names**: `per_page`, `page`
- **Default Page Size**: 100
- **Maximum Page Size**: 100
- **Next Page Determination**: Increment `page` parameter until no data is returned

### Data Extraction
- **JSONPath**: `$.data`
- **Primary Key**: `id`
- **Key Fields**: `id`, `name`, `email`

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `updated_after`
- **Date/Timestamp Field**: `updated_at`
- **Expected Format**: ISO 8601 (e.g., `2023-10-01T00:00:00Z`)
- **Recommended Initial Value**: Current date minus one year

## 4. Endpoint Dependencies

### Resource Relationships
- **Endpoint**: `associated_contacts`
- **Depends On**: `deals`
- **Parameter Mapping**: `deal_id` from `deals.id`
- **Processing Order**: Fetch `deals` first, then `associated_contacts` using `deal_id`

## 5. API Behavior & Limits

### Rate Limiting
- **Requests Per Minute**: 600
- **Burst Limits**: 10 requests per second
- **Recommended Delay**: Use exponential backoff for retries

### Special Requirements
- **Custom Headers**: None required beyond authentication
- **Response Format Considerations**: Ensure JSON parsing is robust to handle null values
- **Error Handling Patterns**: Implement retry logic with exponential backoff for 5xx errors
- **Data Type Specifications**: Ensure correct handling of custom fields with varying data types

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.getbase.com/v2/",
    "auth": {
        "type": "bearer",
        "location": "header",
        "name": "Authorization",
        "format": "Bearer {access_token}"
    }
}

ENDPOINTS = [
    {
        "name": "users",
        "path": "/users",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "limit_param": "per_page",
            "offset_param": "page",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "updated_after",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "associated_contacts", 
        "depends_on": "deals",
        "param_mapping": {"deal_id": "id"}
    }
]
```

This configuration guide provides all necessary parameters and considerations for setting up a dlt data pipeline to effectively extract and manage data from the Zendesk Sell API.

---
*dlt REST API Source Configuration Guide*