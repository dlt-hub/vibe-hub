# Lookml REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source. It outlines the necessary parameters and configurations required to build an effective data pipeline using the dlt framework. The focus is on ensuring seamless integration with REST APIs, enabling efficient data extraction and processing.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.github.com`
- **Version Path**: Not applicable for GitHub API as it uses media types for versioning.

### Authentication
- **Type**: `bearer`
- **Token Format**: `Token {api_token}`
- **Header Name**: `Authorization`
- **Additional Headers**:
  - `User-Agent`: Required for GitHub API requests.
  - `Accept`: `application/vnd.github.v3+json` for specifying the API version.

## 2. Available Endpoints

### Endpoint: Model Files
- **Path**: `/search/code?q=filename:.model.+extension:lkml+repo:[GIT_OWNER]/[GIT_REPOSITORY]`
- **HTTP Method**: `GET`
- **Query Parameters**: 
  - `q`: Search query string.
- **Response Data Structure**: JSON object with `items` key containing the data array.

### Pagination Configuration
- **Type**: `header_link`
- **Next Page Determination**: Extracted from the `Link` header in the response.
- **Default/Maximum Page Sizes**: Not explicitly defined; GitHub API handles pagination internally.

### Data Extraction
- **JSONPath**: `items`
- **Primary Key**: Combination of `git_owner`, `git_repository`, and `path`.

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `If-Modified-Since`
- **Date/Timestamp Field**: `last_modified`
- **Expected Format**: RFC 2822 (e.g., `Sun, 13 Oct 2019 22:40:01 GMT`)
- **Recommended Initial Values**: Use the earliest possible date for the first sync.

## 4. Endpoint Dependencies

### Resource Relationships
- **Parent Endpoint**: `model_files`
- **Child Endpoint**: `models`
- **Mapping**: Use `path` from `model_files` to associate with `models`.
- **Processing Order**: Parent endpoint data must be processed before child endpoint data.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Hour**: 5000 requests per hour for authenticated requests.
- **Burst Limits**: Handle using exponential backoff strategy.

### Special Requirements
- **Custom Headers**: `User-Agent` and `Authorization` are mandatory.
- **Response Format Considerations**: Ensure JSON parsing handles potential null values.
- **Error Handling Patterns**: Implement retries with backoff for 5xx errors and handle specific GitHub error codes.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.github.com",
    "auth": {
        "type": "bearer",
        "name": "Authorization",
        "format": "Token {api_token}"
    },
    "headers": {
        "User-Agent": "{user_agent}",
        "Accept": "application/vnd.github.v3+json"
    }
}

ENDPOINTS = [
    {
        "name": "model_files",
        "path": "/search/code?q=filename:.model.+extension:lkml+repo:[GIT_OWNER]/[GIT_REPOSITORY]",
        "method": "GET",
        "data_selector": "items",
        "primary_key": ["git_owner", "git_repository", "path"],
        "pagination": {
            "type": "header_link"
        },
        "incremental": {
            "cursor_path": "last_modified",
            "param_name": "If-Modified-Since",
            "format": "rfc2822"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "models",
        "depends_on": "model_files",
        "param_mapping": {"path": "path"}
    }
]
```

This configuration guide provides the necessary details to set up a dlt data pipeline for extracting data from GitHub's REST API. It covers client configuration, endpoint details, pagination, incremental loading, and handling dependencies between endpoints.

---
*dlt REST API Source Configuration Guide*