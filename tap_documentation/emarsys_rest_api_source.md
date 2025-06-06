# Emarsys REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation

This document provides a comprehensive guide for configuring a dlt REST API source for the Emarsys API. It includes all necessary parameters and settings to establish a data pipeline that efficiently extracts data from the Emarsys API.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.emarsys.net/api/v2/`
- **Version Path**: `/api/v2/` is included in the base URL.

### Authentication
- **Type**: `custom` (WSSE header-based authentication)
- **Custom Authentication Details**:
  - **Username**: Provided in the configuration
  - **Secret**: Provided in the configuration
  - **WSSE Header**: Constructed using a nonce, created timestamp, and a SHA1 hash of the nonce, created timestamp, and secret.

## 2. Available Endpoints

### Endpoint: Campaigns
- **Path**: `/email/`
- **HTTP Method**: `GET`
- **Query Parameters**: `showdeleted=1` (optional)
- **Response Data Structure**: JSON array of campaign objects
- **Pagination**: Not applicable for this endpoint

### Endpoint: Contacts
- **Path**: `/contact/query/`
- **HTTP Method**: `GET`
- **Query Parameters**:
  - `return=3` (required)
  - `limit` (optional, default 1000)
  - `offset` (optional, default 0)
- **Response Data Structure**: JSON array of contact objects
- **Pagination Configuration**:
  - **Type**: `offset`
  - **Limit Parameter**: `limit`
  - **Offset Parameter**: `offset`
  - **Default Page Size**: 1000

### Endpoint: Contact Lists
- **Path**: `/contactlist`
- **HTTP Method**: `GET`
- **Response Data Structure**: JSON array of contact list objects
- **Pagination**: Not applicable for this endpoint

### Endpoint: Metrics
- **Path**: `/email/responses`
- **HTTP Method**: `POST`
- **Request Body**: JSON object with `type`, `start_date`, `end_date`, `campaign_id`
- **Response Data Structure**: JSON object with metric data
- **Pagination**: Not applicable for this endpoint

## 3. Incremental Data Loading

### Incremental Support for Contacts
- **Query Parameter**: `since`
- **Date Field**: `updated_at`
- **Format**: ISO 8601 date format
- **Initial Value**: Configurable via `start_date` in the configuration

### Incremental Support for Metrics
- **Date Field**: `date`
- **Format**: ISO 8601 date format
- **Initial Value**: Configurable via `start_date` in the configuration

## 4. Endpoint Dependencies

### Resource Relationships
- **Contacts and Contact Lists**: Contact list memberships depend on contact lists.
- **Metrics and Campaigns**: Metrics require campaign data to be fetched first.

### Mapping Identifiers
- **Contact List Memberships**: Use `contact_list_id` and `contact_id` to map memberships.
- **Metrics**: Use `campaign_id` to map metrics to campaigns.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests**: Limited by `X-Ratelimit-Remaining` and `X-Ratelimit-Reset` headers
- **Burst Limits**: Handled by exponential backoff and retry logic

### Special Requirements
- **Custom Headers**: `X-WSSE` for authentication
- **Response Format**: JSON
- **Error Handling**: Retries on 429 (Rate Limit) and 503 (Service Unavailable) errors
- **Data Type Specifications**: Ensure correct parsing of date and numeric fields

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.emarsys.net/api/v2/",
    "auth": {
        "type": "custom",
        "wsse": {
            "username": "{username}",
            "secret": "{secret}"
        }
    }
}

ENDPOINTS = [
    {
        "name": "campaigns",
        "path": "/email/",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id"
    },
    {
        "name": "contacts",
        "path": "/contact/query/",
        "method": "GET",
        "data_selector": "result",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "limit",
            "offset_param": "offset",
            "limit": 1000
        },
        "incremental": {
            "cursor_path": "updated_at",
            "param_name": "since",
            "format": "iso"
        }
    },
    {
        "name": "contact_lists",
        "path": "/contactlist",
        "method": "GET",
        "data_selector": "data",
        "primary_key": "id"
    },
    {
        "name": "metrics",
        "path": "/email/responses",
        "method": "POST",
        "data_selector": "contact_ids",
        "primary_key": ["date", "metric", "contact_id", "campaign_id"]
    }
]

DEPENDENCIES = [
    {
        "endpoint": "contact_list_memberships",
        "depends_on": "contact_lists",
        "param_mapping": {"contact_list_id": "id"}
    },
    {
        "endpoint": "metrics",
        "depends_on": "campaigns",
        "param_mapping": {"campaign_id": "id"}
    }
]
```

This configuration guide provides all necessary details to set up a dlt data pipeline for the Emarsys API, ensuring efficient data extraction and handling of API-specific behaviors.

---
*dlt REST API Source Configuration Guide*