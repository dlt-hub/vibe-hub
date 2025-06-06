# Sharepointsites REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with REST API sources. The configuration is structured to ensure seamless data extraction, transformation, and loading processes.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://graph.microsoft.com/v1.0/`
- This URL serves as the root for all API requests. Ensure that any endpoint paths are appended to this base URL.

### Authentication
- **Type**: `bearer`
- **Token Retrieval**: Uses Azure credentials to obtain a Bearer token.
  - **Credential Options**:
    - `DefaultAzureCredential`: Automatically manages the authentication flow.
    - `ManagedIdentityCredential`: Used when a specific client ID is provided.
- **Header Format**: 
  - `Authorization: Bearer {token}`

## 2. Available Endpoints

### Endpoint: Files
- **Path**: `/drives/{drive_id}/root/children`
- **HTTP Method**: `GET`
- **Query Parameters**: None required for basic listing.
- **Response Data Structure**: JSON with a `value` array containing file metadata.

### Pagination Configuration
- **Type**: `json_link`
- **Next Page Determination**: Use the `@odata.nextLink` field in the response to fetch subsequent pages.
- **Default Page Size**: Determined by API, typically 100 items per page.

### Data Extraction
- **JSONPath**: `$.value[*]`
- **Primary Key**: `id` for unique identification of each file.
- **Data Fields**: Extract fields such as `name`, `lastModifiedDateTime`, and file-specific metadata.

## 3. Incremental Data Loading

### Incremental Support
- **Parameter**: `lastModifiedDateTime`
- **Format**: ISO 8601 (e.g., `2023-10-01T00:00:00Z`)
- **Initial Sync Value**: Use a date far in the past (e.g., `1900-01-01T00:00:00Z`) to ensure all data is captured initially.

## 4. Endpoint Dependencies

### Resource Relationships
- **Files and Folders**: Files are listed under specific drives and folders. Ensure drive IDs are correctly mapped.
- **Mapping**: Use `drive_id` and `folder` parameters to navigate the hierarchy.

## 5. API Behavior & Limits

### Rate Limiting
- **Limits**: Typically defined by Azure, ensure compliance with any specified limits.
- **Burst Limits**: Implement retry logic with exponential backoff to handle rate limit errors.

### Special Requirements
- **Headers**: Ensure `Authorization` header is always included.
- **Response Format**: JSON, handle any potential errors or non-standard responses.
- **Error Handling**: Implement robust error handling to manage HTTP errors and API-specific error codes.

## Output Format

Below is a sample configuration for integrating with the SharePoint Sites API using the dlt framework:

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://graph.microsoft.com/v1.0/",
    "auth": {
        "type": "bearer",
        "location": "header",
        "name": "Authorization",
        "format": "Bearer {token}"
    }
}

ENDPOINTS = [
    {
        "name": "files",
        "path": "/drives/{drive_id}/root/children",
        "method": "GET",
        "data_selector": "value",
        "primary_key": "id",
        "pagination": {
            "type": "json_link",
            "next_link_field": "@odata.nextLink"
        },
        "incremental": {
            "cursor_path": "lastModifiedDateTime",
            "param_name": "since",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "files",
        "depends_on": "drives",
        "param_mapping": {"drive_id": "id"}
    }
]
```

This configuration guide ensures that all necessary parameters and configurations are in place for a successful integration with the REST API, facilitating efficient data extraction and processing.

---
*dlt REST API Source Configuration Guide*