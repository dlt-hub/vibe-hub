# Livechat REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide to configuring a dlt data pipeline for the LiveChat REST API. It includes details on client configuration, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.livechatinc.com`
- **Version**: The API does not specify a version in the URL, but uses headers to manage API versions.

### Authentication
- **Type**: `basic`
- **Parameters**:
  - **Entity ID**: Used as the username in basic authentication.
  - **Access Token**: Used as the password in basic authentication.
- **Headers**:
  - `X-API-Version`: `2` (specifies the API version)

## 2. Available Endpoints

### Endpoint: Chats
- **Path**: `/chats`
- **Method**: `GET`
- **Query Parameters**:
  - `date_from`: Start date for fetching chats (format: `YYYY-MM-DD`)
  - `include_pending`: Whether to include pending chats (0 or 1)
- **Response Data Structure**: JSON object with a key `chats` containing an array of chat records.

### Pagination Configuration
- **Type**: `page_number`
- **Parameters**:
  - `page`: Current page number
- **Determination**: The next page is determined by incrementing the `page` parameter until all records are retrieved.
- **Page Size**: Not explicitly defined, but the API provides `total` and `pages` in the response to manage pagination.

### Data Extraction
- **Data Selector**: `chats`
- **Primary Key**: `id`, `agent_email`
- **Record Identification**: Each chat is uniquely identified by its `id` and `agent_email`.

## 3. Incremental Data Loading

### Incremental Support
- **Parameter**: `date_from`
- **Replication Key**: `ended_timestamp`
- **Format**: ISO 8601 date format (`YYYY-MM-DD`)
- **Initial Value**: Use the `start_date` from the configuration for the first sync.

## 4. Endpoint Dependencies

### Resource Relationships
- **Chats**: Parent stream for `messages` and `events`.
- **Messages and Events**: Require data from `chats` to function correctly.
- **Mapping**: `chat_id` from `chats` is used to associate `messages` and `events`.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests**: The API does not explicitly define rate limits, but it is recommended to handle retries for 5xx errors with exponential backoff.

### Special Requirements
- **Custom Headers**: `X-API-Version` is required to specify the API version.
- **Response Format**: JSON
- **Error Handling**: Implement retries for 5xx errors and handle exceptions for connection issues.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.livechatinc.com",
    "auth": {
        "type": "basic",
        "entity_id": "{entity_id}",
        "access_token": "{access_token}",
        "headers": {
            "X-API-Version": "2"
        }
    }
}

ENDPOINTS = [
    {
        "name": "chats",
        "path": "/chats",
        "method": "GET",
        "data_selector": "chats",
        "primary_key": ["id", "agent_email"],
        "pagination": {
            "type": "page_number",
            "page_param": "page"
        },
        "incremental": {
            "cursor_path": "ended_timestamp",
            "param_name": "date_from",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "messages",
        "depends_on": "chats",
        "param_mapping": {"chat_id": "id"}
    },
    {
        "endpoint": "events",
        "depends_on": "chats",
        "param_mapping": {"chat_id": "id"}
    }
]
```

This configuration guide provides all necessary parameters and considerations for integrating the LiveChat REST API with a dlt data pipeline, ensuring efficient data extraction and synchronization.

---
*dlt REST API Source Configuration Guide*