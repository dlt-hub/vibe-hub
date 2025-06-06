# Mailchimp REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation for Mailchimp

This document provides a comprehensive guide for configuring a dlt data pipeline to extract data from the Mailchimp REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL:** `https://<dc>.api.mailchimp.com/3.0/`
  - Replace `<dc>` with your specific Mailchimp data center identifier.

### Authentication
- **Type:** `bearer`
- **Token Format:** `Bearer {api_key}`
- **Parameter Name:** `Authorization`
- **Location:** Header

## 2. Available Endpoints

### Campaigns
- **Path:** `/campaigns`
- **HTTP Method:** `GET`
- **Response Data Structure:** JSON object with key `campaigns`
- **Primary Key:** `id`

### Reports Email Activity
- **Path:** `/reports/{campaign_id}/email-activity`
- **HTTP Method:** `GET`
- **Response Data Structure:** JSON object with key `emails`
- **Primary Key:** None
- **Parent Endpoint:** `Campaigns`

### Reports Sent To
- **Path:** `/reports/{campaign_id}/sent-to`
- **HTTP Method:** `GET`
- **Response Data Structure:** JSON object with key `sent_to`
- **Primary Key:** `campaign_id`, `email_id`
- **Parent Endpoint:** `Campaigns`

### Lists
- **Path:** `/lists`
- **HTTP Method:** `GET`
- **Response Data Structure:** JSON object with key `lists`
- **Primary Key:** `id`

### Lists Members
- **Path:** `/lists/{list_id}/members`
- **HTTP Method:** `GET`
- **Response Data Structure:** JSON object with key `members`
- **Primary Key:** `id`, `list_id`
- **Parent Endpoint:** `Lists`

### Pagination Configuration
- **Type:** `offset`
- **Parameter Names:** `offset`, `count`
- **Default Page Size:** 1000
- **Determination of Next Page:** Based on the presence of data in the response key

## 3. Incremental Data Loading

### Campaigns
- **Incremental Support:** Not applicable (Full Table)

### Reports Email Activity
- **Incremental Support:** Yes
- **Query Parameter:** `since`
- **Date Field:** `timestamp`
- **Format:** ISO 8601

### Lists Members
- **Incremental Support:** Yes
- **Query Parameter:** `since_last_changed`
- **Date Field:** `last_changed`
- **Format:** ISO 8601

## 4. Endpoint Dependencies

### Reports Email Activity
- **Depends On:** `Campaigns`
- **Parameter Mapping:** `campaign_id` from `Campaigns`

### Reports Sent To
- **Depends On:** `Campaigns`
- **Parameter Mapping:** `campaign_id` from `Campaigns`

### Lists Members
- **Depends On:** `Lists`
- **Parameter Mapping:** `list_id` from `Lists`

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Hour:** Subject to Mailchimp's API rate limits (check Mailchimp documentation for specifics)

### Special Requirements
- **Custom Headers:** `User-Agent` if specified in configuration
- **Response Format Considerations:** Convert empty strings to nulls for proper datetime handling
- **Error Handling Patterns:** Implement retries and backoff strategies for rate limit errors
- **Data Type Specifications:** Ensure correct handling of datetime fields

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://<dc>.api.mailchimp.com/3.0/",
    "auth": {
        "type": "bearer",
        "location": "header",
        "name": "Authorization",
        "format": "Bearer {api_key}"
    }
}

ENDPOINTS = [
    {
        "name": "campaigns",
        "path": "/campaigns",
        "method": "GET",
        "data_selector": "campaigns",
        "primary_key": "id",
        "pagination": {
            "type": "offset",
            "limit_param": "count",
            "offset_param": "offset",
            "limit": 1000
        }
    },
    {
        "name": "reports_email_activity",
        "path": "/reports/{campaign_id}/email-activity",
        "method": "GET",
        "data_selector": "emails",
        "incremental": {
            "cursor_path": "timestamp",
            "param_name": "since",
            "format": "iso"
        }
    },
    {
        "name": "lists_members",
        "path": "/lists/{list_id}/members",
        "method": "GET",
        "data_selector": "members",
        "primary_key": ["id", "list_id"],
        "incremental": {
            "cursor_path": "last_changed",
            "param_name": "since_last_changed",
            "format": "iso"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "reports_email_activity",
        "depends_on": "campaigns",
        "param_mapping": {"campaign_id": "id"}
    },
    {
        "endpoint": "lists_members",
        "depends_on": "lists",
        "param_mapping": {"list_id": "id"}
    }
]
```

This configuration guide provides all necessary parameters and considerations for setting up a dlt data pipeline to extract data from the Mailchimp API effectively. Adjust the configuration as needed based on specific use cases and API updates.

---
*dlt REST API Source Configuration Guide*