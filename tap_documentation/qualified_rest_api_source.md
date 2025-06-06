# Qualified REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source for the "Qualified" API. It includes details on client configuration, available endpoints, incremental data loading, endpoint dependencies, and API behavior and limits.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.qualified.com/v1`
- This is the root URL for all API requests, including the version path.

### Authentication
- **Type**: `bearer`
- **Token Format**: The token is passed in the `Authorization` header in the format `Bearer {token}`.
- **Parameter Name**: `api_key` (used to store the token in the configuration)

### Example Configuration
```python
BASE_CONFIG = {
    "base_url": "https://api.qualified.com/v1",
    "auth": {
        "type": "bearer",
        "location": "header",
        "name": "Authorization",
        "format": "Bearer {token}"
    }
}
```

## 2. Available Endpoints

### Endpoint: Rep Conversations
- **Path**: `/rep_conversations`
- **HTTP Method**: `GET`
- **Primary Key**: `id`
- **Data Selector**: `$.data[*]`

### Endpoint: Bot Conversations
- **Path**: `/bot_conversations`
- **HTTP Method**: `GET`
- **Primary Key**: `id`
- **Data Selector**: `$.data[*]`

### Pagination Configuration
- **Type**: `cursor`
- **Next Page Path**: `$.meta.ending_cursor`
- **Has More Path**: `$.meta.has_more`
- **Parameter Name**: `starting_after`

### Example Endpoint Configuration
```python
ENDPOINTS = [
    {
        "name": "rep_conversations",
        "path": "/rep_conversations",
        "method": "GET",
        "data_selector": "$.data[*]",
        "primary_key": "id",
        "pagination": {
            "type": "cursor",
            "next_page_path": "$.meta.ending_cursor",
            "has_more_path": "$.meta.has_more",
            "param_name": "starting_after"
        }
    },
    {
        "name": "bot_conversations",
        "path": "/bot_conversations",
        "method": "GET",
        "data_selector": "$.data[*]",
        "primary_key": "id",
        "pagination": {
            "type": "cursor",
            "next_page_path": "$.meta.ending_cursor",
            "has_more_path": "$.meta.has_more",
            "param_name": "starting_after"
        }
    }
]
```

## 3. Incremental Data Loading

### Incremental Support
- **Cursor Path**: `updated_at`
- **Parameter Name**: `since`
- **Format**: `iso` (ISO 8601 date format)

### Example Incremental Configuration
```python
"incremental": {
    "cursor_path": "updated_at",
    "param_name": "since",
    "format": "iso"
}
```

## 4. Endpoint Dependencies

### Resource Relationships
- Currently, there are no explicit dependencies between the provided endpoints. However, if future endpoints require data from others, they should be documented here.

### Example Dependency Configuration
```python
DEPENDENCIES = []
```

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Second**: Not explicitly documented; implement a conservative rate limit strategy.
- **Burst Limits**: Implement retry logic with exponential backoff for handling rate limit errors.

### Special Requirements
- **Custom Headers**: `User-Agent` can be customized via configuration.
- **Response Format**: JSON
- **Error Handling**: Implement error handling for common HTTP status codes (e.g., 429 for rate limiting, 500 for server errors).

### Example Special Requirements
```python
"special_requirements": {
    "headers": {
        "User-Agent": "Harness.io/Tap Qualified"
    },
    "error_handling": {
        "retry_on": [429, 500],
        "backoff_strategy": "exponential"
    }
}
```

This configuration guide provides the necessary parameters and structure to effectively integrate and extract data from the Qualified API using a dlt data pipeline. Adjust configurations as needed based on specific use cases or API changes.

---
*dlt REST API Source Configuration Guide*