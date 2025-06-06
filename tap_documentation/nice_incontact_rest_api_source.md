# Nice Incontact REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source for the Nice InContact API. It includes all necessary parameters and configurations to build an effective data pipeline using the dlt framework.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api-{api_cluster}.nice-incontact.com/inContactAPI/services/v{api_version}/`
- **Version Path**: Ensure to replace `{api_cluster}` and `{api_version}` with the appropriate values from your configuration.

### Authentication
- **Type**: `bearer`
- **Token Retrieval**:
  - **Auth Endpoint**: `https://{auth_domain}.nice-incontact.com/authentication/v1/token/access-key`
  - **Refresh Endpoint**: `https://{auth_domain}.nice-incontact.com/public/user/refresh`
  - **Parameters**:
    - `accessKeyId`: Your API key
    - `accessKeySecret`: Your API secret
- **Token Format**: `Bearer {access_token}`

## 2. Available Endpoints

### Contacts Completed
- **Path**: `/contacts/completed`
- **Method**: `GET`
- **Query Parameters**:
  - `updatedSince`: ISO 8601 date format
  - `orderBy`: `lastUpdateTime asc`
  - `skip`: Pagination offset
- **Response Data Structure**: JSON object with `completedContacts` array

### Skills Summary
- **Path**: `/skills/summary`
- **Method**: `GET`
- **Query Parameters**:
  - `startDate`: ISO 8601 date format
  - `endDate`: ISO 8601 date format
- **Response Data Structure**: JSON object with `skillSummaries` array

### Pagination Configuration
- **Type**: `offset`
- **Parameter Names**:
  - `skip`: Used to skip records for pagination
- **Default/Maximum Page Sizes**: API limits to 10,000 records per response

## 3. Incremental Data Loading

### Incremental Support
- **Contacts Completed**:
  - **Parameter**: `updatedSince`
  - **Date Field**: `lastUpdateTime`
  - **Format**: ISO 8601
  - **Initial Value**: Use the `start_date` from configuration

### Skills Summary
- **Parameter**: `startDate` and `endDate`
- **Date Field**: `endDate`
- **Format**: ISO 8601
- **Initial Value**: Use the `start_date` from configuration

## 4. Endpoint Dependencies

### Resource Relationships
- **Skills Summary** may depend on data from other endpoints like `contacts_completed` for comprehensive reporting.
- **Mapping Identifiers**: Use `skillId` to map between related resources.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests**: Specific limits are not detailed, but implement exponential backoff for retries.
- **Burst Limits**: Handle `429 Too Many Requests` with backoff.

### Special Requirements
- **Custom Headers**: `User-Agent` must be specified in requests.
- **Response Format**: JSON
- **Error Handling**: Implement backoff for 5xx and 4xx errors, especially `401 Unauthorized` and `429 Too Many Requests`.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api-{api_cluster}.nice-incontact.com/inContactAPI/services/v{api_version}/",
    "auth": {
        "type": "bearer",
        "auth_endpoint": "https://{auth_domain}.nice-incontact.com/authentication/v1/token/access-key",
        "refresh_endpoint": "https://{auth_domain}.nice-incontact.com/public/user/refresh",
        "access_key_id": "{api_key}",
        "access_key_secret": "{api_secret}"
    }
}

ENDPOINTS = [
    {
        "name": "contacts_completed",
        "path": "/contacts/completed",
        "method": "GET",
        "data_selector": "completedContacts",
        "primary_key": "contactId",
        "pagination": {
            "type": "offset",
            "offset_param": "skip",
            "limit": 10000
        },
        "incremental": {
            "cursor_path": "lastUpdateTime",
            "param_name": "updatedSince",
            "format": "iso"
        }
    },
    {
        "name": "skills_summary",
        "path": "/skills/summary",
        "method": "GET",
        "data_selector": "skillSummaries",
        "primary_key": ["skillId", "startDate", "endDate"],
        "incremental": {
            "cursor_path": "endDate",
            "param_name": "startDate",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "skills_summary",
        "depends_on": "contacts_completed",
        "param_mapping": {"skillId": "skillId"}
    }
]
```

This configuration guide provides all necessary details to set up a dlt data pipeline for the Nice InContact API, ensuring efficient data extraction and handling of API-specific behaviors.

---
*dlt REST API Source Configuration Guide*