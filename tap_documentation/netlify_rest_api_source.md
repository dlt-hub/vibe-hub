# Netlify REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to extract data from the Netlify REST API. The configuration includes details on client setup, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.netlify.com/api/v1/`
- This URL serves as the root for all API requests.

### Authentication
- **Type**: `bearer`
- **Header Name**: `Authorization`
- **Token Format**: `Bearer {auth_token}`
- The `auth_token` is required and should be provided in the configuration.

## 2. Available Endpoints

### Sites Endpoint
- **Path**: `/sites`
- **HTTP Method**: `GET`
- **Primary Key**: `id`
- **Response Data Structure**: JSON array of site objects

### Builds Endpoint
- **Path**: `/sites/{site_id}/builds`
- **HTTP Method**: `GET`
- **Primary Key**: `id`
- **Response Data Structure**: JSON array of build objects

### Deploys Endpoint
- **Path**: `/sites/{site_id}/deploys`
- **HTTP Method**: `GET`
- **Primary Key**: `id`
- **Response Data Structure**: JSON array of deploy objects

### Pagination Configuration
- **Type**: `page_number`
- **Parameter Name**: `page`
- **Default Page Size**: Not explicitly defined, but pagination is handled via the `Link` header or inferred page numbers.
- **Next Page Determination**: Uses the `Link` header if available, otherwise increments page number.

### Data Extraction
- **JSONPath**: `$[*]`
- **Data Location**: The data is located at the root of the JSON response.
- **Key Fields**: `id` for each record type (sites, builds, deploys).

## 3. Incremental Data Loading

- **Incremental Support**: Not explicitly defined in the current configuration. The API does not provide a direct incremental loading mechanism.
- **Recommended Approach**: Use the `updated_at` field if available in the response for custom incremental loading logic.

## 4. Endpoint Dependencies

### Resource Relationships
- **Sites**: Parent resource for both Builds and Deploys.
- **Builds and Deploys**: Require `site_id` from the Sites endpoint.
- **Mapping**: `site_id` is used to fetch builds and deploys related to a specific site.

## 5. API Behavior & Limits

### Rate Limiting
- **Details**: Not explicitly defined in the provided code. Check Netlify API documentation for rate limits.

### Special Requirements
- **Custom Headers**: `User-Agent` can be customized via configuration.
- **Response Format**: JSON
- **Error Handling**: Implement retry logic for handling transient errors and respect HTTP status codes.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.netlify.com/api/v1/",
    "auth": {
        "type": "bearer",
        "location": "header",
        "name": "Authorization",
        "format": "Bearer {auth_token}"
    }
}

ENDPOINTS = [
    {
        "name": "sites",
        "path": "/sites",
        "method": "GET",
        "data_selector": "$[*]",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "param_name": "page"
        }
    },
    {
        "name": "builds",
        "path": "/sites/{site_id}/builds",
        "method": "GET",
        "data_selector": "$[*]",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "param_name": "page"
        }
    },
    {
        "name": "deploys",
        "path": "/sites/{site_id}/deploys",
        "method": "GET",
        "data_selector": "$[*]",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "param_name": "page"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "builds",
        "depends_on": "sites",
        "param_mapping": {"site_id": "id"}
    },
    {
        "endpoint": "deploys",
        "depends_on": "sites",
        "param_mapping": {"site_id": "id"}
    }
]
```

This configuration guide provides the necessary parameters and structure to set up a dlt data pipeline for the Netlify API, ensuring efficient data extraction and integration.

---
*dlt REST API Source Configuration Guide*