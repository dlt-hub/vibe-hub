# Kustomer REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation for tap-kustomer

This document provides a comprehensive guide for configuring the `tap-kustomer` REST API source using dlt. It outlines the necessary parameters and configurations required to build an effective data pipeline for extracting data from the Kustomer API.

## 1. Client Configuration

### Base URL
- **Base URL**: The API base URL depends on the production point of deployment for your organization instance.
  - US Endpoint: `https://api.kustomerapp.com/v1/`
  - EU Endpoint: `https://api.prod2.kustomerapp.com/v1/`

### Authentication
- **Type**: `bearer`
- **Header Name**: `Authorization`
- **Token Format**: `Bearer {api_key}`
- **Configuration Parameter**: `api_key` (required)

## 2. Available Endpoints

### Endpoint Details

#### Companies
- **Path**: `/companies`
- **Method**: `POST`
- **Query Context**: `company`
- **Response Data Structure**: JSON array located at `data`

#### Conversations
- **Path**: `/conversations`
- **Method**: `POST`
- **Query Context**: `conversation`
- **Response Data Structure**: JSON array located at `data`

#### Customers
- **Path**: `/customers`
- **Method**: `POST`
- **Query Context**: `customer`
- **Response Data Structure**: JSON array located at `data`

### Pagination Configuration
- **Type**: `json_link`
- **Next Page Determination**: Based on `links.next` in the JSON response
- **Default Page Size**: 100

### Data Extraction
- **JSONPath**: `$.data[*]`
- **Primary Key**: `id`
- **Unique Identifier**: `id`

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `updated_at`
- **Date Field**: `updated_at` in API responses
- **Format**: ISO 8601
- **Initial Value**: Configurable via `start_date` parameter

## 4. Endpoint Dependencies

### Resource Relationships
- **Attachments**: Depends on `Conversations`
  - **Path**: `/conversations/{id}/attachments`
  - **Parent ID Mapping**: `id` from `Conversations`

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second**: Not explicitly defined; adhere to best practices by implementing exponential backoff on rate limit errors.

### Special Requirements
- **Custom Headers**: `Accept: application/json`
- **User-Agent**: Configurable via `user_agent` parameter
- **Error Handling**: Implement retry logic for transient errors and handle pagination links carefully.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.kustomerapp.com/v1/",
    "auth": {
        "type": "bearer",
        "name": "Authorization",
        "format": "Bearer {api_key}"
    }
}

ENDPOINTS = [
    {
        "name": "companies",
        "path": "/companies",
        "method": "POST",
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "json_link",
            "next_link_jsonpath": "$.links.next",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "updated_at",
            "format": "iso"
        }
    },
    {
        "name": "conversations",
        "path": "/conversations",
        "method": "POST",
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "json_link",
            "next_link_jsonpath": "$.links.next",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "updated_at",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "attachments",
        "depends_on": "conversations",
        "param_mapping": {"id": "id"}
    }
]
```

This configuration guide provides the necessary details to set up a dlt data pipeline for the Kustomer API, ensuring efficient data extraction and incremental updates. Adjust the configuration parameters as needed to fit your specific use case and API limits.

---
*dlt REST API Source Configuration Guide*