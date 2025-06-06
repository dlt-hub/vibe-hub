# Servicem8 REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the ServiceM8 REST API. The configuration includes details on client setup, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.servicem8.com/api_1.0`
- This URL serves as the root for all API requests.

### Authentication
- **Type**: `basic`
- **Credentials**: Requires a username and password for authentication.
- **Header Format**: Basic authentication is handled via the `Authorization` header, which encodes the username and password in base64.

## 2. Available Endpoints

### Endpoint Details

#### Categories
- **Path**: `/category.json`
- **Method**: `GET`
- **Query Parameters**: None required
- **Response Data Structure**: JSON array of category objects

#### Companies
- **Path**: `/company.json`
- **Method**: `GET`
- **Query Parameters**: None required
- **Response Data Structure**: JSON array of company objects

#### Jobs
- **Path**: `/job.json`
- **Method**: `GET`
- **Query Parameters**: `$filter=edit_date gt '{start}'` for incremental loading
- **Response Data Structure**: JSON array of job objects

### Pagination Configuration
- **Type**: `single_page`
- The API does not support pagination; all data is returned in a single response.

### Data Extraction
- **JSONPath**: The data is located directly in the JSON response array.
- **Primary Key**: Each record is uniquely identified by the `uuid` field.

## 3. Incremental Data Loading

### Incremental Support
- **Parameter**: `$filter=edit_date gt '{start}'`
- **Date Field**: `edit_date`
- **Format**: ISO 8601 date format (e.g., `YYYY-MM-DD`)
- **Initial Value**: Use the current date minus a reasonable buffer period for the first sync.

## 4. Endpoint Dependencies

### Resource Relationships
- **Jobs and Job Materials**: The `job_materials` endpoint requires job IDs from the `jobs` endpoint.
- **Mapping**: Use the `job_id` field from `jobs` to fetch related `job_materials`.

## 5. API Behavior & Limits

### Rate Limiting
- **Limits**: The API documentation does not specify rate limits, but it is advisable to implement a retry mechanism with exponential backoff in case of rate limiting errors.

### Special Requirements
- **Custom Headers**: None required beyond authentication.
- **Response Format**: JSON
- **Error Handling**: Implement error handling for HTTP status codes 401 (Unauthorized), 403 (Forbidden), and 404 (Not Found).

## Output Format

Below is a sample configuration for integrating with the ServiceM8 API using dlt:

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.servicem8.com/api_1.0",
    "auth": {
        "type": "basic",
        "username": "your_username",
        "password": "your_password"
    }
}

ENDPOINTS = [
    {
        "name": "categories",
        "path": "/category.json",
        "method": "GET",
        "data_selector": "[]",
        "primary_key": "uuid",
        "pagination": {
            "type": "single_page"
        }
    },
    {
        "name": "jobs",
        "path": "/job.json",
        "method": "GET",
        "data_selector": "[]",
        "primary_key": "uuid",
        "incremental": {
            "cursor_path": "edit_date",
            "param_name": "$filter",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "job_materials",
        "depends_on": "jobs",
        "param_mapping": {"job_id": "uuid"}
    }
]
```

This configuration guide provides all necessary parameters and considerations for setting up a dlt data pipeline to extract data from the ServiceM8 API effectively. Adjust the configuration as needed based on specific use cases and API updates.

---
*dlt REST API Source Configuration Guide*