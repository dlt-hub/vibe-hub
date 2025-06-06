# Workday Raas REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source, specifically for the Workday RaaS (Reports as a Service) API. The configuration will enable you to build an effective data pipeline using dlt to extract data from the Workday RaaS API.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.workday.com/raas/v1/`
- **Version Path**: Ensure to include the version path (`v1`) as part of the base URL.

### Authentication
- **Type**: `basic`
- **Parameters**:
  - **Username**: Your Workday API username.
  - **Password**: Your Workday API password.
- **Usage**: Basic authentication requires the username and password to be encoded in Base64 and included in the `Authorization` header.

## 2. Available Endpoints

### Endpoint Details

#### Users Endpoint
- **Path**: `/users`
- **Method**: `GET`
- **Query Parameters**: None required for basic data retrieval.
- **Response Data Structure**: JSON object with a `data` array containing user records.

#### Example Endpoint Configuration
```python
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
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "since",
            "format": "iso"
        }
    }
]
```

### Pagination Configuration
- **Type**: `offset`
- **Parameters**:
  - **Limit Parameter**: `limit`
  - **Offset Parameter**: `offset`
- **Next Page Determination**: Increment the `offset` by the `limit` value until no more data is returned.
- **Default/Maximum Page Sizes**: Typically 100 records per page.

### Data Extraction
- **JSONPath**: Use `data` to locate the array of user records in the response.
- **Record Identification**: Use the `id` field as the primary key to uniquely identify each user record.

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `since`
- **Date/Timestamp Field**: `updated_at`
- **Expected Format**: ISO 8601 date format (e.g., `2023-10-01T00:00:00Z`)
- **Initial Values for First Sync**: Use the earliest possible date or a date slightly before the expected data range.

## 4. Endpoint Dependencies

### Resource Relationships
- **Dependencies**: Some endpoints may require data from others. For example, `user_posts` may depend on `users`.
- **Mapping Identifiers**: Use `user_id` from the `users` endpoint to fetch related posts from `user_posts`.
- **Processing Order**: Ensure `users` is processed before `user_posts`.

### Example Dependency Configuration
```python
DEPENDENCIES = [
    {
        "endpoint": "user_posts", 
        "depends_on": "users",
        "param_mapping": {"user_id": "id"}
    }
]
```

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second/Minute/Hour**: Follow Workday's API documentation for specific rate limits.
- **Burst Limits**: Implement delays between requests if necessary to avoid hitting rate limits.

### Special Requirements
- **Custom Headers**: None required beyond standard authentication headers.
- **Response Format Considerations**: Ensure the response is in JSON format.
- **Error Handling Patterns**: Implement retry logic for transient errors and handle HTTP status codes appropriately.
- **Data Type Specifications**: Ensure data types in your schema match those expected by the API (e.g., strings for dates).

## Conclusion

This configuration guide provides the necessary parameters and considerations for integrating with the Workday RaaS API using dlt. By following these guidelines, you can effectively set up a data pipeline to extract and process data from the API.

---
*dlt REST API Source Configuration Guide*