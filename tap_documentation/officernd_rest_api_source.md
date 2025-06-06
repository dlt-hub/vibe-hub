# Officernd REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the Officernd REST API. It includes details on client configuration, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://app.officernd.com/api/v1/organizations/saltbox`
- This URL includes the version path and organization identifier.

### Authentication
- **Type**: `oauth2`
- **Token URL**: `https://identity.officernd.com/oauth/token`
- **Required Scopes**: `officernd.api.read`
- **Parameters**:
  - `client_id`: Provided in the configuration
  - `client_secret`: Provided in the configuration
  - `grant_type`: `client_credentials`

## 2. Available Endpoints

### General Endpoint Structure
Each endpoint is accessed via a specific path relative to the base URL. The HTTP method used is typically `GET`.

### Example Endpoint: Members
- **Path**: `/members`
- **HTTP Method**: `GET`
- **Required Query Parameters**: None
- **Response Data Structure**: JSON array located at the root level

### Pagination Configuration
- **Type**: `single_page` (No pagination required for most endpoints)
- **Parameter Names**: Not applicable
- **Default/Maximum Page Sizes**: Not applicable

### Data Extraction
- **JSONPath**: Root level for most endpoints
- **Key Fields**: `_id` (unique identifier for each record)

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `since`
- **Date/Timestamp Field**: `date_modified` or `updated_at` (depending on the endpoint)
- **Expected Format**: ISO 8601 date format
- **Recommended Initial Values**: Use the current date minus a reasonable buffer period for the first sync

## 4. Endpoint Dependencies

### Resource Relationships
- **Example**: The `posts` endpoint may depend on `members` for user-related data.
- **Mapping Identifiers**: Use `user_id` from `members` to filter `posts`.
- **Processing Order**: Fetch `members` before `posts`.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second**: Not explicitly documented; implement a retry mechanism with exponential backoff.
- **Burst Limits**: Not explicitly documented; monitor API responses for rate limit headers.

### Special Requirements
- **Custom Headers**: `Content-Type: application/json`
- **Response Format Considerations**: Ensure JSON parsing is robust to handle unexpected data structures.
- **Error Handling Patterns**: Implement retries for 5xx errors and handle 4xx errors gracefully.
- **Data Type Specifications**: Ensure correct data types are used for fields like dates and identifiers.

## Output Format

Below is a sample configuration for the Officernd API using the dlt framework:

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://app.officernd.com/api/v1/organizations/saltbox",
    "auth": {
        "type": "oauth2",
        "token_url": "https://identity.officernd.com/oauth/token",
        "client_id": "your_client_id",
        "client_secret": "your_client_secret",
        "scope": "officernd.api.read"
    }
}

ENDPOINTS = [
    {
        "name": "members",
        "path": "/members",
        "method": "GET",
        "data_selector": "root",
        "primary_key": "_id",
        "pagination": {
            "type": "single_page"
        },
        "incremental": {
            "cursor_path": "date_modified",
            "param_name": "since",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "posts",
        "depends_on": "members",
        "param_mapping": {"user_id": "_id"}
    }
]
```

This configuration guide should help you set up a robust data pipeline using dlt to extract data from the Officernd API efficiently. Adjust the parameters as needed based on your specific use case and API documentation updates.

---
*dlt REST API Source Configuration Guide*