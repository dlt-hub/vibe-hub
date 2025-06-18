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
  - `redirect_uri`: The redirect URI configured in your application.
  - `refresh_token`: The refresh token obtained from the OAuth2 flow.
  - `access_token`: The access token used for API requests.

## 2. Available Endpoints

### Endpoint Details

#### Example Endpoint: Employees
- **Path**: `/resource/Employee/QUERY`
- **HTTP Method**: `POST`
- **Required Query Parameters**:
  - `search`: JSON object to filter records (e.g., `{"s1": {"field": "Modified", "type": "ge", "data": "2023-01-01T00:00:00Z"}}`)
  - `sort`: JSON object to sort records (e.g., `{"Modified": "asc"}`)
  - `start`: Offset for pagination
  - `max`: Maximum number of records to return
- **Response Data Structure**: JSON array of employee records

### Pagination Configuration
- **Type**: `offset`
- **Parameter Names**:
  - `limit_param`: `max`
  - `offset_param`: `start`
- **Default/Maximum Page Sizes**: Typically 500 records per page
- **Next Page Determination**: Increase `start` by `max` until fewer records than `max` are returned.

### Data Extraction
- **JSONPath to Data**: Directly in the response array
- **Primary Key**: `Id`
- **Key Fields**: `Id`, `Modified`

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `search` with `Modified` field
- **Date/Timestamp Field**: `Modified`
- **Expected Format**: ISO 8601 date-time strings (e.g., `2023-01-01T00:00:00Z`)
- **Recommended Initial Values**: Use the `start_date` from the configuration

## 4. Endpoint Dependencies

### Resource Relationships
- **Example Dependency**: `Employee` data may be required before `EmployeeRole` data.
- **Mapping Identifiers**: Use `Id` from `Employee` to map to `EmployeeRole`.
- **Processing Order**: Fetch `Employee` data first, then `EmployeeRole`.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second/Minute/Hour**: Not explicitly documented; implement exponential backoff for retries.
- **Burst Limits**: Implement retry logic with backoff for handling 5xx errors.

### Special Requirements
- **Custom Headers**: `Authorization: OAuth {access_token}`
- **Response Format Considerations**: Handle JSON responses, including potential null values in JSON fields.
- **Error Handling Patterns**: Retry on 5xx errors and handle 401 errors by refreshing the access token.
- **Data Type Specifications**: Use the `TYPE_MAP` for field type conversions.

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
        "name": "employees",
        "path": "/resource/Employee/QUERY",
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
        "endpoint": "employee_roles", 
        "depends_on": "employees",
        "param_mapping": {"employee_id": "Id"}
    }
]
```

This configuration guide provides the necessary parameters and considerations for setting up a dlt data pipeline to extract data from the Deputy REST API effectively. Adjust the configuration as needed based on specific use cases and API changes.