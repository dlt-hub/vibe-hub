# Kanbanize REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source for the Kanbanize API. It outlines the necessary parameters and configurations required to build an effective data pipeline using the Kanbanize API.

## 1. Client Configuration

**Base URL:**
- Base URL: `https://{subdomain}.kanbanize.com/index.php/api/kanbanize/`
- Note: Replace `{subdomain}` with your specific Kanbanize subdomain.

**Authentication:**
- Type: `api_key`
- Location: Header
- Parameter Name: `apikey`
- Format: The API key should be included in the request headers as `apikey: {your_api_key}`

## 2. Available Endpoints

### Endpoint: Get All Tasks

**Endpoint Details:**
- Path: `/get_all_tasks/boardid/{boardid}/format/json`
- HTTP Method: POST
- Required Query Parameters:
  - `boardid`: The ID of the board from which to retrieve tasks.
- Response Data Structure: JSON array of task objects.

**Pagination Configuration:**
- Type: `single_page`
- Note: The Kanbanize API does not support pagination for the `get_all_tasks` endpoint. All tasks are returned in a single response.

**Data Extraction:**
- JSONPath: The data is located directly in the response as an array of task objects.
- Primary Key: `taskid` (unique identifier for each task)

## 3. Incremental Data Loading

**Incremental Support:**
- The Kanbanize API does not natively support incremental data loading for the `get_all_tasks` endpoint. All tasks must be retrieved and filtered client-side based on timestamps or other criteria.

## 4. Endpoint Dependencies

**Resource Relationships:**
- The `get_all_tasks` endpoint does not have direct dependencies on other endpoints. However, it requires the `boardid` parameter, which may be obtained from other endpoints or configurations.

## 5. API Behavior & Limits

**Rate Limiting:**
- The Kanbanize API documentation does not specify explicit rate limits. It is recommended to implement retry logic and exponential backoff in case of rate limiting or server errors.

**Special Requirements:**
- Custom Headers: Ensure the `apikey` is included in the request headers.
- Response Format: The response is in JSON format.
- Error Handling: Implement error handling for HTTP status codes and unexpected responses.
- Data Type Specifications: Ensure that the data types in the response match the expected schema.

## Output Format

Below is the configuration format for the Kanbanize API using dlt:

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://{subdomain}.kanbanize.com/index.php/api/kanbanize/",
    "auth": {
        "type": "api_key",
        "location": "header",
        "name": "apikey"
    }
}

ENDPOINTS = [
    {
        "name": "tasks",
        "path": "/get_all_tasks/boardid/{boardid}/format/json",
        "method": "POST",
        "data_selector": None,  # Data is directly in the response
        "primary_key": "taskid",
        "pagination": {
            "type": "single_page"
        },
        "incremental": {
            "cursor_path": None,
            "param_name": None,
            "format": None
        }
    }
]

DEPENDENCIES = []
```

## Focus Areas

1. **API Communication**: Ensure the API key is included in the headers for authentication.
2. **Data Location**: The task data is located directly in the JSON response.
3. **Pagination**: The `get_all_tasks` endpoint does not support pagination.
4. **Incremental Updates**: Incremental loading must be handled client-side.
5. **Resource Dependencies**: The `boardid` parameter is required and may need to be sourced from other configurations or endpoints.

This configuration guide provides the necessary details to set up a dlt data pipeline for the Kanbanize API, ensuring efficient data extraction and integration.

---
*dlt REST API Source Configuration Guide*