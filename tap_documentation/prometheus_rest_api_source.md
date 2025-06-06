# Prometheus REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with a REST API source. The configuration is structured to ensure seamless data extraction, transformation, and loading processes.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.prometheus.io/v1/`
- **Version Path**: `/v1/` (ensure to include this in all endpoint paths)

### Authentication
- **Type**: `basic`
- **Username**: Provided in the configuration
- **Password**: Provided in the configuration
- **Parameter Names**: Use standard HTTP Basic Authentication headers

## 2. Available Endpoints

### Endpoint Details

#### Users Endpoint
- **Path**: `/users`
- **HTTP Method**: `GET`
- **Required Query Parameters**: None
- **Optional Query Parameters**: `limit`, `offset`
- **Response Data Structure**: JSON array under the key `data`

#### Metrics Endpoint
- **Path**: `/metrics`
- **HTTP Method**: `GET`
- **Required Query Parameters**: `query`, `start`, `end`, `step`
- **Response Data Structure**: JSON array under the key `data`

### Pagination Configuration

#### Users Endpoint
- **Type**: `offset`
- **Limit Parameter**: `limit`
- **Offset Parameter**: `offset`
- **Default Page Size**: 100
- **Maximum Page Size**: 1000
- **Next Page Determination**: Increment `offset` by `limit` until no more data is returned

### Data Extraction

#### Users Endpoint
- **JSONPath**: `$.data[*]`
- **Primary Key**: `id`
- **Key Fields**: `id`, `name`, `email`

#### Metrics Endpoint
- **JSONPath**: `$.data[*]`
- **Primary Key**: `date`, `labels`
- **Key Fields**: `date`, `labels`, `value`

## 3. Incremental Data Loading

### Incremental Support

#### Metrics Endpoint
- **Query Parameter**: `start`
- **Date/Timestamp Field**: `date`
- **Expected Format**: ISO 8601 (e.g., `2023-10-01T00:00:00Z`)
- **Recommended Initial Value**: Use the `start_date` from the configuration

## 4. Endpoint Dependencies

### Resource Relationships

#### User Posts Endpoint
- **Depends On**: Users Endpoint
- **Parameter Mapping**: `user_id` from Users Endpoint to `user_id` in User Posts Endpoint
- **Required Processing Order**: Fetch users first, then fetch posts using `user_id`

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Minute**: 60
- **Burst Limits**: 10 requests per second
- **Recommended Delays**: Implement a delay of 1 second between bursts

### Special Requirements
- **Custom Headers**: None required beyond authentication
- **Response Format Considerations**: Ensure JSON parsing handles nested structures
- **Error Handling Patterns**: Retry on 5xx errors, log and skip on 4xx errors
- **Data Type Specifications**: Ensure numeric fields are parsed correctly, handle nulls gracefully

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.prometheus.io/v1/",
    "auth": {
        "type": "basic",
        "username": "your_username",
        "password": "your_password"
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
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "offset",
            "limit": 100
        }
    },
    {
        "name": "metrics",
        "path": "/metrics",
        "method": "GET",
        "data_selector": "data",
        "primary_key": ["date", "labels"],
        "incremental": {
            "cursor_path": "date",
            "param_name": "start",
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

This configuration guide provides all necessary parameters and settings to effectively integrate and extract data from the specified REST API using a dlt data pipeline. Adjust the configuration as needed to match specific API requirements or changes.

---
*dlt REST API Source Configuration Guide*