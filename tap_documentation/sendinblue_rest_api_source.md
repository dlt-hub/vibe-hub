# Sendinblue REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the Sendinblue REST API. The configuration includes details on client setup, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.sendinblue.com/v3`
- This is the root URL for all API requests to the Sendinblue service.

### Authentication
- **Type**: `api_key`
- **Location**: `header`
- **Parameter Name**: `api-key`
- **Format**: The API key should be included in the request headers as `api-key: {your_api_key}`.

## 2. Available Endpoints

### Lists Endpoint
- **Path**: `/contacts/lists`
- **HTTP Method**: `GET`
- **Response Data Structure**: JSON array located at `$.lists[*]`
- **Primary Key**: `id`

### Campaigns Endpoint
- **Path**: `/emailCampaigns`
- **HTTP Method**: `GET`
- **Response Data Structure**: JSON array located at `$.campaigns[*]`
- **Primary Key**: `id`

### List Members Endpoint
- **Path**: `/contacts/lists/{list_id}/contacts`
- **HTTP Method**: `GET`
- **Response Data Structure**: JSON array located at `$.contacts[*]`
- **Primary Key**: `id`
- **Parent Stream**: `ListsStream`

### Pagination Configuration
- **Type**: `page_number`
- **Parameter Names**: `page`
- **Next Page Determination**: The next page token is extracted from the JSON response using the JSONPath `$.next_page`.
- **Default/Maximum Page Sizes**: Not explicitly defined, adjust based on API limits.

## 3. Incremental Data Loading

### Incremental Support
- **Lists and Campaigns Streams**: No incremental loading support.
- **List Members Stream**: No incremental loading support.

## 4. Endpoint Dependencies

### Resource Relationships
- **List Members Endpoint**: Depends on the `Lists` endpoint to provide `list_id` for fetching members.
- **Parameter Mapping**: `list_id` from `ListsStream` is used to populate `{list_id}` in the `ListMembersStream` path.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second/Minute/Hour**: Not explicitly defined in the provided code. Refer to Sendinblue API documentation for specific rate limits.
- **Burst Limits and Recommended Delays**: Implement retry logic with exponential backoff to handle rate limits gracefully.

### Special Requirements
- **Custom Headers**: Include `User-Agent` if specified in the configuration.
- **Response Format Considerations**: Ensure JSON response parsing is robust to handle any variations.
- **Error Handling Patterns**: Implement error handling to manage HTTP errors and API-specific error codes.
- **Data Type Specifications**: Ensure data types in the schema match the expected API response types.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.sendinblue.com/v3",
    "auth": {
        "type": "api_key",
        "location": "header",
        "name": "api-key",
        "format": "{api_key}"
    }
}

ENDPOINTS = [
    {
        "name": "lists",
        "path": "/contacts/lists",
        "method": "GET",
        "data_selector": "$.lists[*]",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "page_param": "page"
        }
    },
    {
        "name": "campaigns",
        "path": "/emailCampaigns",
        "method": "GET",
        "data_selector": "$.campaigns[*]",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "page_param": "page"
        }
    },
    {
        "name": "listmembers",
        "path": "/contacts/lists/{list_id}/contacts",
        "method": "GET",
        "data_selector": "$.contacts[*]",
        "primary_key": "id",
        "parent_stream_type": "lists"
    }
]

DEPENDENCIES = [
    {
        "endpoint": "listmembers",
        "depends_on": "lists",
        "param_mapping": {"list_id": "id"}
    }
]
```

This configuration guide provides the necessary parameters and structure to set up a dlt data pipeline for the Sendinblue API, ensuring efficient data extraction and integration.

---
*dlt REST API Source Configuration Guide*