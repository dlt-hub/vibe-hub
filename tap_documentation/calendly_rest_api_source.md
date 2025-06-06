# Calendly REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation for Calendly

This document provides a comprehensive guide to configuring a dlt data pipeline for the Calendly REST API. It includes details on client configuration, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.calendly.com/`
- **Version Path**: Not applicable as the base URL already includes the versioning.

### Authentication
- **Type**: `bearer`
- **Token Format**: `Bearer {token}`
- **Required Header**: 
  - `Authorization`: The bearer token is included in the header.
- **Parameter Names**: 
  - `auth_token`: Used in the configuration to store the bearer token.

## 2. Available Endpoints

### Scheduled Events Endpoint
- **Path**: `/scheduled_events`
- **HTTP Method**: `GET`
- **Required Query Parameters**: 
  - `organization`: The organization URL is required.
- **Response Data Structure**: JSON array located at `$.collection[*]`

### Invitees Endpoint
- **Path**: `/scheduled_events/{uuid}/invitees`
- **HTTP Method**: `GET`
- **Required Query Parameters**: 
  - `organization`: The organization URL is required.
- **Response Data Structure**: JSON array located at `$.collection[*]`

### Pagination Configuration
- **Type**: `cursor`
- **Parameter Names**: 
  - `page_token`: Used to fetch the next page.
- **Next Page Determination**: 
  - JSONPath: `$.pagination.next_page_token`
- **Default/Maximum Page Sizes**: Not explicitly defined in the configuration.

### Data Extraction
- **JSONPath**: `$.collection[*]` for both endpoints.
- **Primary Key**: 
  - `ScheduledEvents`: `uri`
  - `Invitees`: `uri`
- **Key Fields**: `uri` uniquely identifies each record.

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: Not explicitly defined for incremental fetching.
- **Date/Timestamp Field**: `updated_at`
- **Expected Format**: ISO 8601 format with timezone information.
- **Recommended Initial Values**: Use the earliest possible date or a specific starting point based on business requirements.

## 4. Endpoint Dependencies

### Resource Relationships
- **Invitees Endpoint**: Depends on the `ScheduledEvents` endpoint.
- **Mapping Identifiers**: 
  - `uuid` from `ScheduledEvents` is used in the path for `Invitees`.
- **Processing Order**: Fetch `ScheduledEvents` first, then `Invitees` using the `uuid`.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second/Minute/Hour**: Not explicitly defined in the configuration. Refer to Calendly's API documentation for specific rate limits.
- **Burst Limits and Recommended Delays**: Implement retry logic with exponential backoff as a best practice.

### Special Requirements
- **Custom Headers**: 
  - `accept`: `application/json`
  - `Content-Type`: `application/json`
- **Response Format Considerations**: Ensure JSON parsing handles potential errors.
- **Error Handling Patterns**: Log errors and implement retry logic for non-200 responses.
- **Data Type Specifications**: Ensure correct parsing of datetime fields with timezone information.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.calendly.com/",
    "auth": {
        "type": "bearer",
        "name": "Authorization",
        "format": "Bearer {token}"
    }
}

ENDPOINTS = [
    {
        "name": "scheduled_events",
        "path": "/scheduled_events",
        "method": "GET",
        "data_selector": "$.collection[*]",
        "primary_key": "uri",
        "pagination": {
            "type": "cursor",
            "next_page_token_jsonpath": "$.pagination.next_page_token"
        },
        "incremental": {
            "cursor_path": "updated_at",
            "format": "iso"
        }
    },
    {
        "name": "invitees",
        "path": "/scheduled_events/{uuid}/invitees",
        "method": "GET",
        "data_selector": "$.collection[*]",
        "primary_key": "uri",
        "pagination": {
            "type": "cursor",
            "next_page_token_jsonpath": "$.pagination.next_page_token"
        },
        "incremental": {
            "cursor_path": "updated_at",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "invitees", 
        "depends_on": "scheduled_events",
        "param_mapping": {"uuid": "uri"}
    }
]
```

This configuration guide provides all necessary parameters and considerations for integrating the Calendly REST API with a dlt data pipeline. Adjust the configuration as needed based on specific use cases and API updates.

---
*dlt REST API Source Configuration Guide*