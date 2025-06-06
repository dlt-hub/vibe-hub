# Teamwork REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source for the Teamwork API. It outlines the necessary parameters and settings required to build an effective data pipeline using the dlt framework.

## 1. Client Configuration

### Base URL
- **Base URL**: The base URL for the Teamwork API is constructed using the hostname provided in the configuration. The default path for most endpoints is `"/projects/api/v3/"`, but some endpoints require a different base path.
  - Example: `https://yourcompany.teamwork.com/projects/api/v3/`

### Authentication
- **Type**: `basic`
- **Details**:
  - The API uses Basic Authentication with the API key as the username and a dummy password.
  - The `Authorization` header is constructed using Base64 encoding of the API key and dummy password.
  - Example Header: `Authorization: Basic {base64_encoded_api_key:xxx}`

## 2. Available Endpoints

### Endpoint Details

#### Companies
- **Path**: `/companies.json`
- **Method**: `GET`
- **Response Data Structure**: JSON object with a key `companies` containing the data array.

#### Latest Activity
- **Path**: `/latestactivity.json`
- **Method**: `GET`
- **Response Data Structure**: JSON object with a key `activities`.

#### Projects
- **Path**: `/projects.json`
- **Method**: `GET`
- **Response Data Structure**: JSON object with a key `projects`.

### Pagination Configuration
- **Type**: `page_number`
- **Parameters**:
  - `page`: Indicates the current page number.
  - `pageSize`: Number of records per page (default is 250).
- **Next Page Determination**: If the number of results equals the page size, increment the page number.

### Data Extraction
- **JSONPath**: Use the `response_result_key` to locate the data array in the response.
- **Primary Key**: Each stream specifies a primary key, typically `id`, to uniquely identify records.

## 3. Incremental Data Loading

### Incremental Support
- **Parameter**: `updatedAfter`
- **Date Field**: Responses include a timestamp field such as `updated_at` for tracking updates.
- **Format**: ISO 8601 date format.
- **Initial Sync**: Use the `start_date` configuration parameter to specify the initial date for data extraction.

## 4. Endpoint Dependencies

### Resource Relationships
- **Dependencies**: Some endpoints may depend on data from others. For example, `user_posts` might depend on `users`.
- **Mapping**: Use identifiers such as `user_id` to map between related resources.

## 5. API Behavior & Limits

### Rate Limiting
- **Limits**: The API may impose limits on the number of requests per second/minute/hour. Implement appropriate delays to handle rate limits.

### Special Requirements
- **Custom Headers**: Include headers such as `User-Agent` with a default value of `tap-teamwork@teamwork.com`.
- **Response Format**: Ensure the response is parsed correctly, especially when handling nested JSON structures.
- **Error Handling**: Implement retry logic for transient errors and handle HTTP status codes appropriately.

## Output Format

Below is an example configuration for the Teamwork API using the dlt framework:

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://yourcompany.teamwork.com/projects/api/v3/",
    "auth": {
        "type": "basic",
        "header": "Authorization",
        "format": "Basic {base64_encoded_api_key:xxx}"
    }
}

ENDPOINTS = [
    {
        "name": "companies",
        "path": "/companies.json",
        "method": "GET",
        "data_selector": "companies",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "limit_param": "pageSize",
            "offset_param": "page",
            "limit": 250
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "updatedAfter",
            "format": "iso"
        }
    },
    {
        "name": "projects",
        "path": "/projects.json",
        "method": "GET",
        "data_selector": "projects",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "limit_param": "pageSize",
            "offset_param": "page",
            "limit": 250
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "updatedAfter",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "project_custom_fields",
        "depends_on": "projects",
        "param_mapping": {"project_id": "id"}
    }
]
```

This configuration guide provides the necessary details to set up a dlt data pipeline for the Teamwork API, ensuring efficient data extraction and processing.

---
*dlt REST API Source Configuration Guide*