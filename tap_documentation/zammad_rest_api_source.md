# Zammad REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the Zammad REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://your-zammad-instance.com/api/v1/`
- **Version Path**: `/api/v1/`

### Authentication
- **Type**: `api_key`
- **Location**: `header`
- **Parameter Name**: `Authorization`
- **Format**: `Token token={auth_token}`

## 2. Available Endpoints

### Tickets Endpoint
- **Path**: `/tickets/search`
- **HTTP Method**: `GET`
- **Required Query Parameters**: `query`, `per_page`, `page`
- **Response Data Structure**: JSON with data located at `$.assets.Ticket[*].*`

#### Pagination Configuration
- **Type**: `offset`
- **Limit Parameter**: `per_page`
- **Offset Parameter**: `page`
- **Default/Maximum Page Size**: 200
- **Next Page Determination**: Increment `page` parameter

#### Data Extraction
- **JSONPath**: `$.assets.Ticket[*].*`
- **Primary Key**: `id`
- **Key Fields**: `id`, `updated_at`

### Users Endpoint
- **Path**: `/users/search`
- **HTTP Method**: `GET`
- **Response Data Structure**: JSON with data located at `$[*]`

#### Pagination Configuration
- **Type**: `offset`
- **Limit Parameter**: `per_page`
- **Offset Parameter**: `page`
- **Default/Maximum Page Size**: 200

#### Data Extraction
- **JSONPath**: `$[*]`
- **Primary Key**: `id`
- **Key Fields**: `id`, `updated_at`

### Tags Endpoint
- **Path**: `/tags?object=Ticket&o_id={ticket_id}`
- **HTTP Method**: `GET`
- **Response Data Structure**: JSON with data located at `$.tags`

#### Pagination Configuration
- **Type**: `single_page`

#### Data Extraction
- **JSONPath**: `$.tags`
- **Primary Key**: `ticket_id`

## 3. Incremental Data Loading

### Tickets Stream
- **Incremental Support**: Yes
- **Query Parameter**: `query`
- **Date/Timestamp Field**: `updated_at`
- **Expected Format**: ISO 8601 (e.g., `2023-10-01T12:00:00Z`)
- **Recommended Initial Value**: `updated_at:>0`

### Users Stream
- **Incremental Support**: Yes
- **Query Parameter**: `query`
- **Date/Timestamp Field**: `updated_at`
- **Expected Format**: ISO 8601

## 4. Endpoint Dependencies

### Tags Endpoint
- **Depends On**: Tickets Endpoint
- **Parameter Mapping**: `ticket_id` from Tickets Stream

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second**: Not explicitly defined, but recommended to respect server capacity
- **Burst Limits**: Implement retry logic with exponential backoff

### Special Requirements
- **Custom Headers**: `Authorization: Token token={auth_token}`
- **Response Format**: JSON
- **Error Handling**: Implement retry logic for HTTP 429 and 5xx errors
- **Data Type Specifications**: Ensure correct handling of date and time fields

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://your-zammad-instance.com/api/v1/",
    "auth": {
        "type": "api_key",
        "location": "header",
        "name": "Authorization",
        "format": "Token token={auth_token}"
    }
}

ENDPOINTS = [
    {
        "name": "tickets",
        "path": "/tickets/search",
        "method": "GET",
        "data_selector": "$.assets.Ticket[*].*",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "per_page",
            "offset_param": "page",
            "limit": 200
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "query",
            "format": "iso"
        }
    },
    {
        "name": "users",
        "path": "/users/search",
        "method": "GET",
        "data_selector": "$[*]",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "per_page",
            "offset_param": "page",
            "limit": 200
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "query",
            "format": "iso"
        }
    },
    {
        "name": "tags",
        "path": "/tags?object=Ticket&o_id={ticket_id}",
        "method": "GET",
        "data_selector": "$.tags",
        "primary_key": "ticket_id",
        "pagination": {
            "type": "single_page"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "tags",
        "depends_on": "tickets",
        "param_mapping": {"ticket_id": "id"}
    }
]
```

This configuration guide provides all necessary parameters and settings to effectively integrate and extract data from the Zammad REST API using a dlt data pipeline.

---
*dlt REST API Source Configuration Guide*