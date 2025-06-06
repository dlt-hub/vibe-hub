# Spreadsheets Anywhere REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt REST API source. The configuration is structured to ensure seamless integration and data extraction from REST APIs into a data pipeline.

## 1. Client Configuration

### Base URL
- **Base URL**: The complete API base URL is required, including any version paths or common prefixes. For example, `https://api.example.com/v1/`.

### Authentication
- **Type**: Specify the authentication type. Options include `api_key`, `bearer`, `basic`, `oauth2`, or custom.
  - **API Key**: Provide the name, location (query/header), and parameter name.
  - **Bearer Token**: Specify the token format and requirements.
  - **OAuth2**: Include the `token_url` and required scopes.
- **Parameter Names**: Ensure to provide exact parameter names used in requests.

## 2. Available Endpoints

For each REST API endpoint that provides data, the following details are required:

### Endpoint Details
- **Path**: The path relative to the base URL.
- **HTTP Method**: Typically `GET`.
- **Query Parameters**: List required and optional query parameters.
- **Response Data Structure**: Describe the structure of the response data.

### Pagination Configuration
- **Type**: Specify the pagination type. Options include `offset`, `page_number`, `cursor`, `json_link`, `header_link`, or `single_page`.
- **Parameter Names**: Include parameter names such as `limit`, `offset`, `page`, `cursor`, etc.
- **Next Page Determination**: Explain how the next page is determined from responses.
- **Page Sizes**: Provide default and maximum page sizes.

### Data Extraction
- **JSONPath**: Specify the JSONPath to locate the actual data array in responses (e.g., "data", "results", "items").
- **Record Identification**: Describe how to identify individual records.
- **Key Fields**: List key fields that uniquely identify each record.

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: Specify which query parameter enables incremental data fetching.
- **Date/Timestamp Field**: Identify the date/timestamp field in API responses for tracking updates.
- **Format**: Specify the expected format (ISO dates, Unix timestamps, etc.).
- **Initial Values**: Recommend initial values for the first sync.

## 4. Endpoint Dependencies

### Resource Relationships
- **Dependencies**: Identify which endpoints require data from other endpoints first.
- **Identifier Mapping**: Explain how to map identifiers between parent and child resources.
- **Processing Order**: Specify the required processing order.

## 5. API Behavior & Limits

### Rate Limiting
- **Limits**: Specify requests per second/minute/hour.
- **Burst Limits**: Provide burst limits and recommended delays.

### Special Requirements
- **Custom Headers**: List any required custom headers.
- **Response Format**: Note any response format considerations.
- **Error Handling**: Describe error handling patterns.
- **Data Type Specifications**: Include any data type specifications.

## Output Format

Below is an example configuration format for a dlt REST API source:

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.example.com/v1/",
    "auth": {
        "type": "api_key",
        "location": "header",
        "name": "Authorization",
        "format": "Bearer {token}"
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
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "since",
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

## Focus Areas

1. **API Communication**: Ensure correct HTTP methods, headers, and authentication.
2. **Data Location**: Accurately locate data arrays in JSON responses.
3. **Pagination**: Effectively retrieve all available records across multiple requests.
4. **Incremental Updates**: Efficiently fetch only new or updated records.
5. **Resource Dependencies**: Manage API endpoint dependencies effectively.

This guide aims to provide complete REST API configuration parameters for building an effective dlt data pipeline that can extract all available data from the API.

---
*dlt REST API Source Configuration Guide*