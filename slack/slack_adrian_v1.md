# dlt REST API Source Configuration Documentation for Slack API

This document provides a comprehensive guide for configuring a dlt data pipeline to extract data from the Slack API using REST API integration. The configuration is structured to ensure seamless data extraction, handling authentication, pagination, incremental loading, and endpoint dependencies.

## 1. Client Configuration

**Base URL:**
- `https://slack.com/api/`
- This is the root URL for all Slack API requests.

**Authentication:**
- **Type:** `bearer`
- **Token Format:** `Bearer {token}`
- **Parameter Name:** `Authorization`
- The token is passed in the header for authentication.

## 2. Available Endpoints

### Channels Endpoint

**Endpoint Details:**
- **Path:** `/conversations.list`
- **HTTP Method:** `GET`
- **Required Query Parameters:** `types` (e.g., `public_channel`), `exclude_archived`
- **Response Data Structure:** JSON with a `channels` array

**Pagination Configuration:**
- **Type:** `cursor`
- **Parameter Name:** `cursor`
- **Limit Parameter:** `limit`
- **Default/Maximum Page Size:** 500
- **Next Page Determination:** From `response_metadata.next_cursor` in the response

**Data Extraction:**
- **JSONPath:** `channels.[*]`
- **Primary Key:** `id`

### Channel Members Endpoint

**Endpoint Details:**
- **Path:** `/conversations.members`
- **HTTP Method:** `GET`
- **Required Query Parameters:** `channel`
- **Response Data Structure:** JSON with a `members` array

**Pagination Configuration:**
- **Type:** `cursor`
- **Parameter Name:** `cursor`
- **Limit Parameter:** `limit`
- **Next Page Determination:** From `response_metadata.next_cursor` in the response

**Data Extraction:**
- **JSONPath:** `members.[*]`
- **Primary Key:** Combination of `channel_id` and `member_id`

### Messages Endpoint

**Endpoint Details:**
- **Path:** `/conversations.history`
- **HTTP Method:** `GET`
- **Required Query Parameters:** `channel`, `oldest`
- **Response Data Structure:** JSON with a `messages` array

**Pagination Configuration:**
- **Type:** `cursor`
- **Parameter Name:** `cursor`
- **Limit Parameter:** `limit`
- **Next Page Determination:** From `response_metadata.next_cursor` in the response

**Data Extraction:**
- **JSONPath:** `messages.[*]`
- **Primary Key:** Combination of `channel_id` and `ts`

## 3. Incremental Data Loading

**Incremental Support:**
- **Query Parameter:** `oldest`
- **Date/Timestamp Field:** `ts` (Epoch timestamp)
- **Expected Format:** Unix timestamp
- **Recommended Initial Value:** 0 for full historical sync or a specific timestamp for incremental sync

## 4. Endpoint Dependencies

**Resource Relationships:**
- **Channel Members Endpoint** depends on **Channels Endpoint** to provide `channel_id`.
- **Messages Endpoint** depends on **Channels Endpoint** to provide `channel_id`.

**Mapping Identifiers:**
- Use `channel_id` from the Channels endpoint to query Channel Members and Messages endpoints.

## 5. API Behavior & Limits

**Rate Limiting:**
- **Requests per Minute:** 50 for Messages and Threads streams
- **Recommended Delay:** Implement a delay of `60.0 / max_requests_per_minute` between requests to avoid hitting rate limits.

**Special Requirements:**
- **Custom Headers:** None beyond authentication
- **Response Format Considerations:** Ensure JSON parsing is robust to handle Slack's response structure.
- **Error Handling Patterns:** Check for `ok` field in responses to determine success and handle errors accordingly.
- **Data Type Specifications:** Ensure correct handling of timestamps and other data types as specified in the schemas.

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
            "next_page_token_jsonpath": "$.response_metadata.next_cursor",
            "limit": 500
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
            "next_page_token_jsonpath": "$.response_metadata.next_cursor"
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
            "next_page_token_jsonpath": "$.response_metadata.next_cursor"
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