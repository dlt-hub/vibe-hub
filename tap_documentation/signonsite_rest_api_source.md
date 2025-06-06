# Signonsite REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the SignOnSite REST API. The configuration includes details on client setup, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://app.signonsite.com.au/api/public`
- This URL serves as the root for all API requests.

### Authentication
- **Type**: `bearer`
- **Header Name**: `Authorization`
- **Format**: `Bearer {api_key}`
- The API key is required and should be included in the request headers for authentication.

## 2. Available Endpoints

### Sites Endpoint
- **Path**: `/sites`
- **HTTP Method**: `GET`
- **Query Parameters**: None required for basic data retrieval.
- **Response Data Structure**: JSON object with a `data` array containing site records.

### Attendances Endpoint
- **Path**: `/sites/{site_id}/attendances`
- **HTTP Method**: `GET`
- **Query Parameters**:
  - `offset`: Used for pagination.
  - `limit`: Maximum number of records per request (default is 2000).
  - `filter_start_time`: Used for incremental data loading.
- **Response Data Structure**: JSON object with a `data` array containing attendance records.

### Pagination Configuration
- **Type**: `offset`
- **Limit Parameter**: `limit`
- **Offset Parameter**: `offset`
- **Default/Maximum Page Size**: 2000
- **Next Page Determination**: Use `current_page` and `last_page` fields in the response to determine if more pages are available.

### Data Extraction
- **JSONPath**: `data` (for both sites and attendances)
- **Primary Key**: `id` (for sites, attendances, users, and companies)
- **Key Fields**: `id` uniquely identifies each record.

## 3. Incremental Data Loading

### Incremental Support
- **Parameter**: `filter_start_time`
- **Date Field**: `updated_at` (for attendances)
- **Format**: ISO 8601 (e.g., `2023-10-01T12:00:00`)
- **Initial Value**: Use the current timestamp for the first sync to fetch the latest data.

## 4. Endpoint Dependencies

### Resource Relationships
- **Attendances** depend on **Sites**: Attendances are retrieved for each site.
- **Users** and **Companies** depend on **Attendances**: User and company data are extracted from attendance records.
- **Processing Order**: Sites -> Attendances -> Users/Companies

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second**: Not explicitly defined, but adhere to best practices to avoid throttling.
- **Burst Limits**: Implement retry logic with exponential backoff in case of rate limiting.

### Special Requirements
- **Custom Headers**: `Authorization` header with Bearer token.
- **Response Format**: JSON
- **Error Handling**: Handle HTTP status codes 401 (Unauthorized), 403 (Forbidden), and 404 (Not Found) with appropriate exception handling.
- **Data Type Specifications**: Ensure all date fields are handled in ISO 8601 format.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://app.signonsite.com.au/api/public",
    "auth": {
        "type": "bearer",
        "location": "header",
        "name": "Authorization",
        "format": "Bearer {api_key}"
    }
}

ENDPOINTS = [
    {
        "name": "sites",
        "path": "/sites",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "offset",
            "limit": 2000
        }
    },
    {
        "name": "attendances",
        "path": "/sites/{site_id}/attendances",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "offset",
            "limit": 2000
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "filter_start_time",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "attendances",
        "depends_on": "sites",
        "param_mapping": {"site_id": "id"}
    },
    {
        "endpoint": "users",
        "depends_on": "attendances",
        "param_mapping": {"user_id": "id"}
    },
    {
        "endpoint": "companies",
        "depends_on": "attendances",
        "param_mapping": {"company_id": "id"}
    }
]
```

This configuration guide provides all necessary parameters and considerations for integrating the SignOnSite API into a dlt data pipeline, ensuring efficient data extraction and synchronization.

---
*dlt REST API Source Configuration Guide*