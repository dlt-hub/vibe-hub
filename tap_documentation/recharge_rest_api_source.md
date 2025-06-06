# Recharge REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source for the Recharge API. It includes details on client configuration, available endpoints, incremental data loading, endpoint dependencies, and API behavior and limits.

## 1. Client Configuration

**Base URL:**
- Base URL: `https://api.rechargeapps.com/`
- API Version: `2021-11` (except for the Products endpoint which uses `2021-01`)

**Authentication:**
- Type: `api_key`
- Location: `header`
- Parameter Name: `X-Recharge-Access-Token`
- Format: `Bearer {token}`

## 2. Available Endpoints

For each endpoint, the following details are provided:

### Addresses
- **Path:** `/addresses`
- **HTTP Method:** `GET`
- **Query Parameters:** `sort_by`, `limit`, `cursor`
- **Response Data Structure:** JSON object with `addresses` key

**Pagination Configuration:**
- **Type:** `cursor`
- **Parameter Names:** `limit`, `cursor`
- **Default/Maximum Page Sizes:** 50
- **Next Page Determination:** Use `next_cursor` from the response

**Data Extraction:**
- **JSONPath:** `addresses`
- **Primary Key:** `id`

### Charges
- **Path:** `/charges`
- **HTTP Method:** `GET`
- **Query Parameters:** `sort_by`, `limit`, `cursor`
- **Response Data Structure:** JSON object with `charges` key

**Pagination Configuration:**
- **Type:** `cursor`
- **Parameter Names:** `limit`, `cursor`
- **Default/Maximum Page Sizes:** 50
- **Next Page Determination:** Use `next_cursor` from the response

**Data Extraction:**
- **JSONPath:** `charges`
- **Primary Key:** `id`

### Customers
- **Path:** `/customers`
- **HTTP Method:** `GET`
- **Query Parameters:** `sort_by`, `limit`, `cursor`
- **Response Data Structure:** JSON object with `customers` key

**Pagination Configuration:**
- **Type:** `cursor`
- **Parameter Names:** `limit`, `cursor`
- **Default/Maximum Page Sizes:** 50
- **Next Page Determination:** Use `next_cursor` from the response

**Data Extraction:**
- **JSONPath:** `customers`
- **Primary Key:** `id`

## 3. Incremental Data Loading

**Incremental Support:**
- **Query Parameter:** `sort_by`
- **Date/Timestamp Field:** `updated_at`
- **Format:** ISO 8601
- **Recommended Initial Values:** Use the `start_date` from the configuration

## 4. Endpoint Dependencies

**Resource Relationships:**
- Some endpoints may require data from others, such as `user_posts` depending on `users`.
- **Mapping Identifiers:** Use `user_id` from `users` to fetch related `user_posts`.

## 5. API Behavior & Limits

**Rate Limiting:**
- **Requests per Minute:** 100 requests per minute
- **Burst Limits:** Implement a delay of 5 seconds when rate limit is hit

**Special Requirements:**
- **Custom Headers:** `X-Recharge-Version` for API versioning
- **Response Format Considerations:** JSON
- **Error Handling Patterns:** Custom exceptions for HTTP status codes
- **Data Type Specifications:** Ensure correct data types as per schema

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.rechargeapps.com/",
    "auth": {
        "type": "api_key",
        "location": "header",
        "name": "X-Recharge-Access-Token",
        "format": "Bearer {token}"
    }
}

ENDPOINTS = [
    {
        "name": "addresses",
        "path": "/addresses",
        "method": "GET",
        "data_selector": "addresses",
        "primary_key": "id",
        "pagination": {
            "type": "cursor",
            "limit_param": "limit",
            "cursor_param": "cursor",
            "limit": 50
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "sort_by",
            "format": "iso"
        }
    },
    {
        "name": "charges",
        "path": "/charges",
        "method": "GET",
        "data_selector": "charges",
        "primary_key": "id",
        "pagination": {
            "type": "cursor",
            "limit_param": "limit",
            "cursor_param": "cursor",
            "limit": 50
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "sort_by",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "user_posts", 
        "depends_on": "users",
        "param_mapping": {"user_id": "id"}
    }
]
```

This configuration guide provides the necessary parameters and structure to effectively integrate and extract data from the Recharge API using a dlt data pipeline.

---
*dlt REST API Source Configuration Guide*