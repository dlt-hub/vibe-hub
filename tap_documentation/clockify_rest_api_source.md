# Clockify REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation for Clockify

This document provides a comprehensive guide to configuring a dlt data pipeline for the Clockify REST API. It includes details on client configuration, available endpoints, incremental data loading, endpoint dependencies, and API behavior and limits.

## 1. Client Configuration

**Base URL:**
- Base URL: `https://api.clockify.me/api/v1/`
- Note: The base URL is appended with the workspace ID for specific endpoints.

**Authentication:**
- Type: `api_key`
- Location: `header`
- Parameter Name: `x-api-key`
- Format: The API key is passed directly in the header.

## 2. Available Endpoints

### Clients
- **Path:** `/clients`
- **HTTP Method:** GET
- **Response Data Structure:** JSON array of client objects
- **Pagination Configuration:**
  - Type: `page_number`
  - Parameters: `page`, `page-size`
  - Default Page Size: 100
- **Data Extraction:**
  - JSONPath: `$[*]`
  - Primary Key: `id`

### Projects
- **Path:** `/projects`
- **HTTP Method:** GET
- **Response Data Structure:** JSON array of project objects
- **Pagination Configuration:**
  - Type: `page_number`
  - Parameters: `page`, `page-size`
  - Default Page Size: 100
- **Data Extraction:**
  - JSONPath: `$[*]`
  - Primary Key: `id`

### Tags
- **Path:** `/tags`
- **HTTP Method:** GET
- **Response Data Structure:** JSON array of tag objects
- **Pagination Configuration:**
  - Type: `page_number`
  - Parameters: `page`, `page-size`
  - Default Page Size: 100
- **Data Extraction:**
  - JSONPath: `$[*]`
  - Primary Key: `id`

### Users
- **Path:** `/users`
- **HTTP Method:** GET
- **Response Data Structure:** JSON array of user objects
- **Pagination Configuration:**
  - Type: `page_number`
  - Parameters: `page`, `page-size`
  - Default Page Size: 100
- **Data Extraction:**
  - JSONPath: `$[*]`
  - Primary Key: `id`

### Tasks
- **Path:** `/projects/{project_id}/tasks`
- **HTTP Method:** GET
- **Response Data Structure:** JSON array of task objects
- **Pagination Configuration:**
  - Type: `page_number`
  - Parameters: `page`, `page-size`
  - Default Page Size: 100
- **Data Extraction:**
  - JSONPath: `$[*]`
  - Primary Key: `id`

### Time Entries
- **Path:** `/user/{user_id}/time-entries`
- **HTTP Method:** GET
- **Response Data Structure:** JSON array of time entry objects
- **Pagination Configuration:**
  - Type: `page_number`
  - Parameters: `page`, `page-size`
  - Default Page Size: 100
- **Data Extraction:**
  - JSONPath: `$[*]`
  - Primary Key: `id`

### Workspaces
- **Path:** `/workspaces`
- **HTTP Method:** GET
- **Response Data Structure:** JSON array of workspace objects
- **Pagination Configuration:**
  - Type: `page_number`
  - Parameters: `page`, `page-size`
  - Default Page Size: 100
- **Data Extraction:**
  - JSONPath: `$[*]`
  - Primary Key: `id`

## 3. Incremental Data Loading

**Incremental Support:**
- **Time Entries Stream:**
  - Query Parameter: `start`
  - Date Field: `started_at`
  - Format: ISO 8601 (e.g., `2023-01-01T00:00:00Z`)
  - Recommended Initial Value: The earliest date from which you want to start syncing data.

## 4. Endpoint Dependencies

**Resource Relationships:**
- **Tasks Stream:**
  - Depends on: Projects Stream
  - Parameter Mapping: `project_id` from Projects Stream
- **Time Entries Stream:**
  - Depends on: Users Stream
  - Parameter Mapping: `user_id` from Users Stream

## 5. API Behavior & Limits

**Rate Limiting:**
- The Clockify API does not explicitly document rate limits, but it is recommended to implement retry logic with exponential backoff in case of rate limiting errors.

**Special Requirements:**
- Custom Headers: `x-api-key` for authentication
- Response Format: JSON
- Error Handling: Implement retry logic for HTTP 429 (Too Many Requests) and other transient errors.

This configuration guide provides the necessary parameters and structure to effectively integrate with the Clockify API using a dlt data pipeline. Adjust the configuration as needed based on specific use cases and data requirements.

---
*dlt REST API Source Configuration Guide*