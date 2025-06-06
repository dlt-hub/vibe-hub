# Freshdesk REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation for Freshdesk

This document provides a comprehensive guide for configuring a dlt data pipeline to extract data from the Freshdesk REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://{domain}.freshdesk.com/api/v2/`
  - Replace `{domain}` with your Freshdesk domain (e.g., `skynet` for `skynet.freshdesk.com`).

### Authentication
- **Type**: `basic`
- **Details**:
  - **Username**: API Key (provided by Freshdesk)
  - **Password**: Leave empty
  - **Header**: `Authorization: Basic {base64_encoded_api_key}`

## 2. Available Endpoints

### Agents
- **Path**: `/agents`
- **Method**: `GET`
- **Pagination**: 
  - **Type**: `page_number`
  - **Parameters**: `per_page=100`, `page`
- **Data Extraction**: JSONPath `"$.[*]"`

### Companies
- **Path**: `/companies`
- **Method**: `GET`
- **Pagination**: 
  - **Type**: `page_number`
  - **Parameters**: `per_page=100`, `page`
- **Data Extraction**: JSONPath `"$.[*]"`

### Tickets Abridged
- **Path**: `/tickets`
- **Method**: `GET`
- **Pagination**: 
  - **Type**: `page_number`
  - **Parameters**: `per_page=100`, `page`
- **Incremental**: 
  - **Cursor Path**: `updated_at`
  - **Parameter Name**: `updated_since`
  - **Format**: ISO 8601
- **Data Extraction**: JSONPath `"$.[*]"`

### Tickets Detail
- **Path**: `/tickets/{ticket_id}`
- **Method**: `GET`
- **Data Extraction**: JSONPath `"$.[*]"`

### Conversations
- **Path**: `/tickets/{ticket_id}/conversations`
- **Method**: `GET`
- **Pagination**: 
  - **Type**: `page_number`
  - **Parameters**: `per_page=100`, `page`
- **Data Extraction**: JSONPath `"$.[*]"`

## 3. Incremental Data Loading

### Tickets Abridged
- **Incremental Support**: Yes
- **Query Parameter**: `updated_since`
- **Date Field**: `updated_at`
- **Format**: ISO 8601
- **Initial Value**: Use the `start_date` from configuration

## 4. Endpoint Dependencies

### Tickets Detail
- **Depends On**: Tickets Abridged
- **Parameter Mapping**: `{ticket_id}` from Tickets Abridged

### Conversations
- **Depends On**: Tickets Detail
- **Parameter Mapping**: `{ticket_id}` from Tickets Detail

## 5. API Behavior & Limits

### Rate Limiting
- **Requests**: Follow Freshdesk's rate limits (typically 100 requests per minute)
- **Burst Limits**: Use `Retry-After` header for handling 429 responses

### Special Requirements
- **Headers**: 
  - `User-Agent`: Customizable via configuration
- **Error Handling**: 
  - Handle 429 errors using `Retry-After` header
  - Parse error messages from JSON response

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://{domain}.freshdesk.com/api/v2/",
    "auth": {
        "type": "basic",
        "username": "{api_key}",
        "password": ""
    }
}

ENDPOINTS = [
    {
        "name": "agents",
        "path": "/agents",
        "method": "GET",
        "data_selector": "$.[*]",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "limit_param": "per_page",
            "offset_param": "page",
            "limit": 100
        }
    },
    {
        "name": "tickets_abridged",
        "path": "/tickets",
        "method": "GET",
        "data_selector": "$.[*]",
        "primary_key": "id",
        "pagination": {
            "type": "page_number",
            "limit_param": "per_page",
            "offset_param": "page",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "updated_since",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "tickets_detail",
        "depends_on": "tickets_abridged",
        "param_mapping": {"ticket_id": "id"}
    },
    {
        "endpoint": "conversations",
        "depends_on": "tickets_detail",
        "param_mapping": {"ticket_id": "id"}
    }
]
```

This configuration guide provides all necessary parameters and settings to effectively integrate with the Freshdesk API using a dlt data pipeline. Adjust the configuration as needed based on your specific Freshdesk instance and data requirements.

---
*dlt REST API Source Configuration Guide*