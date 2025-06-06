# Lever REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to extract data from the Lever API. The configuration includes details on client setup, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.lever.co/v1/`
- **Version Path**: `/v1/`

### Authentication
- **Type**: `basic`
- **Details**:
  - **Username**: API token
  - **Password**: Leave empty
- **Header**: `Authorization: Basic {base64_encoded_token}`

## 2. Available Endpoints

### Endpoint: Candidates
- **Path**: `/candidates`
- **HTTP Method**: `GET`
- **Query Parameters**: None required
- **Response Data Structure**: JSON array under `data`

### Pagination Configuration
- **Type**: `offset`
- **Parameters**:
  - **Limit**: `limit`
  - **Offset**: `offset`
- **Default Page Size**: 100
- **Next Page Determination**: Use `next` field in response

### Data Extraction
- **JSONPath**: `data`
- **Primary Key**: `id`

## 3. Incremental Data Loading

### Incremental Support
- **Parameter**: `updated_at_start` and `updated_at_end`
- **Date Field**: `updated_at`
- **Format**: Unix timestamp (milliseconds)
- **Initial Value**: Use `start_date` from configuration

## 4. Endpoint Dependencies

### Resource Relationships
- **Endpoint**: `/candidates/{candidate_id}/referrals`
  - **Depends On**: `/candidates`
  - **Parameter Mapping**: `candidate_id` from `candidates.id`

- **Endpoint**: `/candidates/{candidate_id}/applications`
  - **Depends On**: `/candidates`
  - **Parameter Mapping**: `candidate_id` from `candidates.id`

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Minute**: 60
- **Burst Limit**: 10 requests per second
- **Recommended Delay**: Implement exponential backoff for retries

### Special Requirements
- **Custom Headers**: `Content-Type: application/json`
- **Error Handling**: Retry on 5xx errors, log and skip on 4xx errors
- **Data Type Specifications**: Ensure date fields are parsed as ISO 8601 strings

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.lever.co/v1/",
    "auth": {
        "type": "basic",
        "username": "{api_token}",
        "password": ""
    }
}

ENDPOINTS = [
    {
        "name": "candidates",
        "path": "/candidates",
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
            "param_name": "updated_at_start",
            "format": "unix"
        }
    },
    {
        "name": "candidate_referrals",
        "path": "/candidates/{candidate_id}/referrals",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id",
        "dependencies": {
            "parent": "candidates",
            "param_mapping": {"candidate_id": "id"}
        }
    },
    {
        "name": "candidate_applications",
        "path": "/candidates/{candidate_id}/applications",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id",
        "dependencies": {
            "parent": "candidates",
            "param_mapping": {"candidate_id": "id"}
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "candidate_referrals",
        "depends_on": "candidates",
        "param_mapping": {"candidate_id": "id"}
    },
    {
        "endpoint": "candidate_applications",
        "depends_on": "candidates",
        "param_mapping": {"candidate_id": "id"}
    }
]
```

This configuration guide ensures that all necessary parameters are defined for a successful integration with the Lever API using dlt. Adjust the configuration as needed based on specific API changes or additional endpoints.

---
*dlt REST API Source Configuration Guide*