# Recruitee REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation for tap-recruitee

This document provides a comprehensive guide for configuring the `tap-recruitee` REST API source to build an effective data pipeline using dlt. The configuration includes details on client setup, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.recruitee.com/c/{company_id}`
  - Replace `{company_id}` with the actual company ID provided in the configuration.

### Authentication
- **Type**: `bearer`
- **Token Format**: `Bearer {auth_token}`
  - The `auth_token` is required and should be provided in the configuration.
- **Header**: `Authorization: Bearer {auth_token}`

## 2. Available Endpoints

### Candidate Stream
- **Path**: `/candidates`
- **HTTP Method**: `GET`
- **Data Selector**: `$.candidates[*]`
- **Primary Key**: `id`

### Offer Stream
- **Path**: `/offers`
- **HTTP Method**: `GET`
- **Data Selector**: `$.offers[*]`
- **Primary Key**: `id`

### Departments Stream
- **Path**: `/departments`
- **HTTP Method**: `GET`
- **Data Selector**: `$.departments[*]`
- **Primary Key**: `id`

### Pagination Configuration
- **Type**: `page_number`
- **Parameter Names**: `page`
- **Default Page Size**: Not explicitly defined; depends on API defaults.
- **Next Page Determination**: Based on the `$.next_page` JSON path in the response.

## 3. Incremental Data Loading

### Candidate Stream
- **Incremental Support**: Yes
- **Cursor Path**: `updated_at`
- **Parameter Name**: `since`
- **Format**: ISO 8601 date format
- **Recommended Initial Value**: Use a date far in the past to ensure all data is captured initially.

### Offer Stream
- **Incremental Support**: Yes
- **Cursor Path**: `updated_at`
- **Parameter Name**: `since`
- **Format**: ISO 8601 date format

### Departments Stream
- **Incremental Support**: Not applicable as departments may not frequently change.

## 4. Endpoint Dependencies

### Resource Relationships
- **Candidate Stream**: May depend on `departments` for department IDs.
- **Offer Stream**: May depend on `departments` for department IDs.

### Mapping Identifiers
- **Candidate to Department**: Use `department_id` from candidates to map to departments.
- **Offer to Department**: Use `department_id` from offers to map to departments.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second**: Not explicitly defined; adhere to general best practices for API usage.
- **Burst Limits**: Implement exponential backoff in case of rate limit errors.

### Special Requirements
- **Custom Headers**: `User-Agent` can be specified in the configuration.
- **Response Format**: JSON
- **Error Handling**: Implement retry logic for transient errors and handle HTTP status codes appropriately.
- **Data Type Specifications**: Ensure data types match the schema definitions provided in the `schemas` directory.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.recruitee.com/c/{company_id}",
    "auth": {
        "type": "bearer",
        "name": "Authorization",
        "format": "Bearer {auth_token}"
    }
}

ENDPOINTS = [
    {
        "name": "candidates",
        "path": "/candidates",
        "method": "GET",
        "data_selector": "$.candidates[*]",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "page_param": "page"
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "since",
            "format": "iso"
        }
    },
    {
        "name": "offers",
        "path": "/offers",
        "method": "GET",
        "data_selector": "$.offers[*]",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "page_param": "page"
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "since",
            "format": "iso"
        }
    },
    {
        "name": "departments",
        "path": "/departments",
        "method": "GET",
        "data_selector": "$.departments[*]",
        "primary_key": "id"
    }
]

DEPENDENCIES = [
    {
        "endpoint": "candidates",
        "depends_on": "departments",
        "param_mapping": {"department_id": "id"}
    },
    {
        "endpoint": "offers",
        "depends_on": "departments",
        "param_mapping": {"department_id": "id"}
    }
]
```

This configuration guide provides all necessary parameters and considerations for setting up a dlt data pipeline with the `tap-recruitee` API source. Ensure to replace placeholders with actual values and adjust configurations based on specific use cases and API behavior.

---
*dlt REST API Source Configuration Guide*