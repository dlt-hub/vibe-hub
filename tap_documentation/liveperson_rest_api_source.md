# Liveperson REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation for LivePerson API

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the LivePerson REST API. It includes detailed instructions on client configuration, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.liveperson.net/api/account/{account_id}/`
- **Version Path**: Ensure to replace `{account_id}` with your specific account ID.

### Authentication
- **Type**: `oauth1`
- **Parameters**:
  - `app_key`: Your application key.
  - `app_secret`: Your application secret.
  - `access_token`: Your access token.
  - `access_token_secret`: Your access token secret.
- **Authorization Method**: OAuth1 is used for authentication, requiring the above parameters to be included in each request.

## 2. Available Endpoints

### Users Endpoint
- **Path**: `/configuration/le-users/users?include_deleted=true`
- **HTTP Method**: `GET`
- **Query Parameters**: `include_deleted` (optional, default is `true`)
- **Response Data Structure**: JSON array of user objects.

### Skills Endpoint
- **Path**: `/configuration/le-users/skills?include_deleted=true`
- **HTTP Method**: `GET`
- **Query Parameters**: `include_deleted` (optional, default is `true`)
- **Response Data Structure**: JSON array of skill objects.

### Messaging Interactions Endpoint
- **Path**: `/messaging_history/api/account/{account_id}/conversations/search?v=2`
- **HTTP Method**: `POST`
- **Query Parameters**: None
- **Response Data Structure**: JSON array of conversation history records.

## Pagination Configuration

### Users and Skills Endpoints
- **Type**: `offset`
- **Parameters**:
  - `limit`: Maximum number of records per request (default: 100).
  - `offset`: Starting point for the next set of records.
- **Next Page Determination**: Increment the `offset` by the `limit` value until no more records are returned.

### Messaging Interactions Endpoint
- **Type**: `json_link`
- **Parameters**: Pagination is handled internally by the API's metadata.
- **Next Page Determination**: Use the `_metadata` field to determine if more pages are available.

## 3. Incremental Data Loading

### Incremental Support
- **Users and Skills Endpoints**: Not applicable as these endpoints do not support incremental loading.
- **Messaging Interactions Endpoint**:
  - **Query Parameter**: `start` and `end` timestamps.
  - **Date Field**: `updated_at` in ISO 8601 format.
  - **Initial Value**: Use the current date minus a reasonable interval (e.g., 7 days) for the first sync.

## 4. Endpoint Dependencies

### Resource Relationships
- **Users and Skills**: Independent endpoints with no dependencies.
- **Messaging Interactions**: May require user and skill data for comprehensive analysis.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests**: Typically limited to a certain number of requests per minute/hour. Check your account's specific rate limits.
- **Burst Limits**: Implement retry logic with exponential backoff to handle rate limit errors.

### Special Requirements
- **Custom Headers**: `Content-Type: application/json` is required for POST requests.
- **Response Format**: JSON format is standard.
- **Error Handling**: Implement error handling for HTTP status codes and API-specific error messages.
- **Data Type Specifications**: Ensure correct data types are used when processing API responses.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.liveperson.net/api/account/{account_id}/",
    "auth": {
        "type": "oauth1",
        "app_key": "{app_key}",
        "app_secret": "{app_secret}",
        "access_token": "{access_token}",
        "access_token_secret": "{access_token_secret}"
    }
}

ENDPOINTS = [
    {
        "name": "users",
        "path": "/configuration/le-users/users?include_deleted=true",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "offset",
            "limit": 100
        }
    },
    {
        "name": "skills",
        "path": "/configuration/le-users/skills?include_deleted=true",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "offset",
            "limit": 100
        }
    },
    {
        "name": "messaging_interactions",
        "path": "/messaging_history/api/account/{account_id}/conversations/search?v=2",
        "method": "POST",
        "data_selector": "conversationHistoryRecords",
        "primary_key": "info.conversationId",
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "start",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "messaging_interactions",
        "depends_on": ["users", "skills"],
        "param_mapping": {"user_id": "id", "skill_id": "id"}
    }
]
```

This configuration guide provides the necessary parameters and structure to effectively integrate the LivePerson API with a dlt data pipeline, ensuring comprehensive data extraction and synchronization.

---
*dlt REST API Source Configuration Guide*