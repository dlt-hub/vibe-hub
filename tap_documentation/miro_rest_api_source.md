# Miro REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the Miro REST API. The configuration includes details on client setup, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.miro.com`
- **Version Path**: `/v2/`

### Authentication
- **Type**: `bearer`
- **Token Format**: `Bearer {token}`
- **Required Parameter**: `access_token` (provided in the configuration)

### HTTP Headers
- **Accept**: `application/json`
- **User-Agent**: Customizable via configuration

## 2. Available Endpoints

### Organization Members
- **Path**: `/v2/orgs/{organization_id}/members`
- **HTTP Method**: `GET`
- **Required Query Parameters**: None by default, but can be customized
- **Response Data Structure**: JSON array located at `$.data[*]`

### Pagination Configuration
- **Type**: `cursor`
- **Cursor Parameter**: `cursor`
- **Limit Parameter**: `limit`
- **Default Limit**: 100
- **Next Page Token**: Extracted from `$.data[-1:].id`

### Data Extraction
- **JSONPath**: `$.data[*]`
- **Primary Key**: `id`

## 3. Incremental Data Loading

### Incremental Support
- **Cursor Path**: `updated_at` (not explicitly used in the current configuration)
- **Parameter Name**: `since` (not explicitly used in the current configuration)
- **Format**: ISO 8601 dates (recommended for future implementation)

## 4. Endpoint Dependencies

### Resource Relationships
- **Dependencies**: None explicitly defined, but the `organization_id` is required for accessing the `organization_members` endpoint.

## 5. API Behavior & Limits

### Rate Limiting
- **Rate Limit Headers**: `X-RateLimit-Reset` used for backoff strategy
- **Recommended Delay**: Calculated based on `X-RateLimit-Reset` header

### Special Requirements
- **Custom Headers**: None beyond standard authentication and content type
- **Response Format**: JSON
- **Error Handling**: Implement backoff strategy based on rate limit headers

## Example Configuration

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.miro.com/v2/",
    "auth": {
        "type": "bearer",
        "name": "Authorization",
        "format": "Bearer {token}"
    }
}

ENDPOINTS = [
    {
        "name": "organization_members",
        "path": "/v2/orgs/{organization_id}/members",
        "method": "GET",
        "data_selector": "$.data[*]",
        "primary_key": "id",
        "pagination": {
            "type": "cursor",
            "limit_param": "limit",
            "cursor_param": "cursor",
            "limit": 100
        }
    }
]

DEPENDENCIES = []
```

This configuration guide provides the necessary parameters and structure to set up a dlt data pipeline for the Miro API, ensuring efficient data extraction and integration. Adjust the configuration as needed to accommodate specific use cases or additional endpoints.

---
*dlt REST API Source Configuration Guide*