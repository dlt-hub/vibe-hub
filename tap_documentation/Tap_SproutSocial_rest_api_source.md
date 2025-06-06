# Tap Sproutsocial REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation for Tap-SproutSocial

This document provides a comprehensive guide for configuring the dlt data pipeline to integrate with the SproutSocial REST API. It covers all necessary parameters and configurations to effectively extract data from the API.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.sproutsocial.com/{version}/{customer_id}`
  - Replace `{version}` with the API version, typically `v1`.
  - Replace `{customer_id}` with the specific customer ID.

### Authentication
- **Type**: `bearer`
- **Token Name**: `token_name`
  - The token is passed in the `Authorization` header in the format `Bearer {token}`.
- **Header**: `Authorization`

## 2. Available Endpoints

### Customer Profiles
- **Path**: `/metadata/customer`
- **Method**: `GET`
- **Primary Key**: `customer_profile_id`
- **Data Selector**: `$.data.[*]`

### Customer Tags
- **Path**: `/metadata/customer/tags`
- **Method**: `GET`
- **Primary Key**: `tag_id`
- **Data Selector**: `$.data.[*]`

### Post Analytics
- **Path**: `/analytics/posts`
- **Method**: `POST`
- **Primary Key**: `guid`
- **Data Selector**: `$.data.[*]`

#### Pagination Configuration
- **Type**: `cursor`
- **Cursor Field**: `guid`
- **Limit**: 100 (Default: 50, Max: 100)
- **Pagination Strategy**: Use the last `guid` from the response as the cursor for the next request.

## 3. Incremental Data Loading

### Incremental Support for Post Analytics
- **Cursor Parameter**: `guid`
- **Date Field**: `created_time`
- **Date Format**: ISO 8601 (e.g., `2023-10-01T00:00:00`)
- **Initial Sync**: Use the `start_date` configuration parameter to define the earliest record date to sync.

## 4. Endpoint Dependencies

### Resource Relationships
- **Post Analytics** depends on **Customer Profiles**:
  - **Mapping**: `customer_profile_id` from Customer Profiles is used in Post Analytics filters.
  - **Processing Order**: Fetch Customer Profiles first to obtain `customer_profile_id` for Post Analytics.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests**: Follow SproutSocial's API documentation for specific rate limits.
- **Burst Limits**: Implement delays as necessary to comply with rate limits.

### Special Requirements
- **Headers**: `Content-Type: application/json`
- **Response Format**: JSON
- **Error Handling**: Implement retry logic for transient errors and handle HTTP status codes appropriately.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.sproutsocial.com/v1/{customer_id}",
    "auth": {
        "type": "bearer",
        "name": "Authorization",
        "format": "Bearer {token}"
    }
}

ENDPOINTS = [
    {
        "name": "customer_profiles",
        "path": "/metadata/customer",
        "method": "GET",
        "data_selector": "$.data.[*]",
        "primary_key": "customer_profile_id"
    },
    {
        "name": "customer_tags",
        "path": "/metadata/customer/tags",
        "method": "GET",
        "data_selector": "$.data.[*]",
        "primary_key": "tag_id"
    },
    {
        "name": "post_analytics",
        "path": "/analytics/posts",
        "method": "POST",
        "data_selector": "$.data.[*]",
        "primary_key": "guid",
        "pagination": {
            "type": "cursor",
            "cursor_field": "guid",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "created_time",
            "param_name": "start_date",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "post_analytics", 
        "depends_on": "customer_profiles",
        "param_mapping": {"customer_profile_id": "customer_profile_id"}
    }
]
```

This configuration guide provides all necessary details to set up a dlt data pipeline for the SproutSocial API, ensuring efficient data extraction and integration.

---
*dlt REST API Source Configuration Guide*