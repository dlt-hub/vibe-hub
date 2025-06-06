# Deputy REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to extract data from the Deputy REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior considerations.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://{domain}/api/v1/`
- **Version Path**: `/v1/` is included in the base URL.

### Authentication
- **Type**: `oauth2`
- **Token URL**: `https://{domain}/oauth/access_token`
- **Required Scopes**: `longlife_refresh_token`
- **Parameters**:
  - `client_id`: Your application's client ID.
  - `client_secret`: Your application's client secret.
  - `redirect_uri`: The URI to redirect to after authentication.
  - `refresh_token`: The token used to obtain a new access token.
  - `access_token`: The token used for API requests.

## 2. Available Endpoints

### Endpoint Details

#### Example Endpoint: `contacts`
- **Path**: `/resource/Contact/QUERY`
- **HTTP Method**: `POST`
- **Required Query Parameters**:
  - `search`: JSON object for filtering data.
  - `sort`: JSON object for sorting data.
  - `start`: Offset for pagination.
  - `max`: Maximum number of records to return.
- **Response Data Structure**: JSON array of contact objects.

### Pagination Configuration
- **Type**: `offset`
- **Parameters**:
  - `limit_param`: `max`
  - `offset_param`: `start`
- **Default/Maximum Page Sizes**: Typically 500 records per page.
- **Next Page Determination**: If the number of records returned is less than the `max`, there are no more pages.

### Data Extraction
- **JSONPath**: The data is located directly in the response array.
- **Primary Key**: `Id` field uniquely identifies each record.

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `search` with `Modified` field.
- **Date/Timestamp Field**: `Modified`
- **Expected Format**: ISO 8601 date-time strings.
- **Recommended Initial Values**: Use the `start_date` from the configuration.

## 4. Endpoint Dependencies

### Resource Relationships
- **Example Dependency**: `employee_agreements` may depend on `employees`.
- **Mapping Identifiers**: Use `employee_id` from `employees` to fetch related `employee_agreements`.
- **Processing Order**: Fetch parent resources (e.g., `employees`) before child resources (e.g., `employee_agreements`).

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second/Minute/Hour**: Not explicitly documented, but typical REST API best practices apply.
- **Burst Limits and Recommended Delays**: Implement exponential backoff for retries.

### Special Requirements
- **Custom Headers**: `Authorization: OAuth {access_token}`
- **Response Format Considerations**: Handle JSON responses with potential null fields.
- **Error Handling Patterns**: Implement retries for 5xx errors and token refresh for 401 errors.
- **Data Type Specifications**: Ensure correct handling of data types as specified in the API schema.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://{domain}/api/v1/",
    "auth": {
        "type": "oauth2",
        "token_url": "https://{domain}/oauth/access_token",
        "client_id": "{client_id}",
        "client_secret": "{client_secret}",
        "redirect_uri": "{redirect_uri}",
        "refresh_token": "{refresh_token}",
        "access_token": "{access_token}"
    }
}

ENDPOINTS = [
    {
        "name": "contacts",
        "path": "/resource/Contact/QUERY",
        "method": "POST",
        "data_selector": "[]",
        "primary_key": "Id",
        "pagination": {
            "type": "offset",
            "limit_param": "max",
            "offset_param": "start",
            "limit": 500
        },
        "incremental": {
            "cursor_path": "Modified",
            "param_name": "search",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "employee_agreements",
        "depends_on": "employees",
        "param_mapping": {"employee_id": "Id"}
    }
]
```

This configuration guide provides the necessary parameters and structure to set up a dlt data pipeline for the Deputy REST API, ensuring efficient data extraction and integration.

---
*dlt REST API Source Configuration Guide*