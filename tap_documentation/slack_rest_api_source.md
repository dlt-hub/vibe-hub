# Slack REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation for Slack API

This document provides a comprehensive guide to configuring a dlt data pipeline for extracting data from the Slack API using REST API integration. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- `https://slack.com/api/`
- This is the base URL for all Slack API requests.

**Authentication:**
- **Type:** `bearer`
- **Token Format:** `Bearer {token}`
- **Parameter Name:** `Authorization`
- The token is passed in the header for authentication.

## 2. Available Endpoints

### Channels

**Endpoint Details:**
- **Path:** `/conversations.list`
- **HTTP Method:** `GET`
- **Required Query Parameters:** `exclude_archived`, `types`
- **Response Data Structure:** JSON with a `channels` array

**Pagination Configuration:**
- **Type:** `cursor`
- **Parameter Name:** `cursor`
- **Limit Parameter:** `limit`
- **Default Page Size:** 500
- **Next Page Token:** Extracted from `$.response_metadata.next_cursor`

**Data Extraction:**
- **JSONPath:** `channels.[*]`
- **Primary Key:** `id`

### Channel Members

**Endpoint Details:**
- **Path:** `/conversations.members`
- **HTTP Method:** `GET`
- **Required Query Parameters:** `channel`
- **Response Data Structure:** JSON with a `members` array

**Pagination Configuration:**
- **Type:** `cursor`
- **Parameter Name:** `cursor`
- **Limit Parameter:** `limit`
- **Default Page Size:** 500
- **Next Page Token:** Extracted from `$.response_metadata.next_cursor`

**Data Extraction:**
- **JSONPath:** `members.[*]`
- **Primary Key:** Combination of `channel_id` and `member_id`

### Messages

**Endpoint Details:**
- **Path:** `/conversations.history`
- **HTTP Method:** `GET`
- **Required Query Parameters:** `channel`
- **Response Data Structure:** JSON with a `messages` array

**Pagination Configuration:**
- **Type:** `cursor`
- **Parameter Name:** `cursor`
- **Limit Parameter:** `limit`
- **Default Page Size:** 500
- **Next Page Token:** Extracted from `$.response_metadata.next_cursor`

**Data Extraction:**
- **JSONPath:** `messages.[*]`
- **Primary Key:** Combination of `channel_id` and `ts`

## 3. Incremental Data Loading

**Incremental Support:**
- **Query Parameter:** `oldest`
- **Date/Timestamp Field:** `ts` (Epoch timestamp)
- **Expected Format:** Unix timestamp
- **Recommended Initial Value:** Use the `start_date` from configuration or `0` for full historical sync.

## 4. Endpoint Dependencies

**Resource Relationships:**
- **Channel Members** depend on **Channels** to provide `channel_id`.
- **Messages** depend on **Channels** to provide `channel_id`.

**Mapping Identifiers:**
- **Channel Members:** Map `channel_id` from Channels.
- **Messages:** Map `channel_id` from Channels.

## 5. API Behavior & Limits

**Rate Limiting:**
- **Requests per Minute:** 200
- **Burst Limits:** Implement a delay of `60.0 / max_requests_per_minute` between requests.

**Special Requirements:**
- **Custom Headers:** None beyond authentication.
- **Response Format Considerations:** Ensure JSON parsing.
- **Error Handling Patterns:** Check for `ok` field in responses and handle errors accordingly.
- **Data Type Specifications:** Ensure correct data types as per schema definitions.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://slack.com/api/",
    "auth": {
        "type": "bearer",
        "name": "Authorization",
        "format": "Bearer {token}"
    }
}

ENDPOINTS = [
    {
        "name": "channels",
        "path": "/conversations.list",
        "method": "GET",
        "data_selector": "channels",
        "primary_key": "id",
        "pagination": {
            "type": "cursor",
            "limit_param": "limit",
            "cursor_param": "cursor",
            "limit": 500
        },
        "incremental": {
            "cursor_path": "ts",
            "param_name": "oldest",
            "format": "unix"
        }
    },
    {
        "name": "channel_members",
        "path": "/conversations.members",
        "method": "GET",
        "data_selector": "members",
        "primary_key": ["channel_id", "member_id"],
        "pagination": {
            "type": "cursor",
            "limit_param": "limit",
            "cursor_param": "cursor",
            "limit": 500
        }
    },
    {
        "name": "messages",
        "path": "/conversations.history",
        "method": "GET",
        "data_selector": "messages",
        "primary_key": ["channel_id", "ts"],
        "pagination": {
            "type": "cursor",
            "limit_param": "limit",
            "cursor_param": "cursor",
            "limit": 500
        },
        "incremental": {
            "cursor_path": "ts",
            "param_name": "oldest",
            "format": "unix"
        }
    }
]

DEPENDENCIES = [
    {
        "endpoint": "channel_members", 
        "depends_on": "channels",
        "param_mapping": {"channel_id": "id"}
    },
    {
        "endpoint": "messages", 
        "depends_on": "channels",
        "param_mapping": {"channel_id": "id"}
    }
]
```

This configuration guide provides all necessary parameters and considerations for setting up a dlt data pipeline to extract data from the Slack API efficiently.

---
*dlt REST API Source Configuration Guide*