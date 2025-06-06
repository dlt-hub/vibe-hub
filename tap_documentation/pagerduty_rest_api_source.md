# Pagerduty REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to extract data from the PagerDuty REST API. It covers client configuration, available endpoints, incremental data loading, endpoint dependencies, and API behavior and limits.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.pagerduty.com`
- **Version**: The API uses versioning in headers, not in the URL path.

### Authentication
- **Type**: `api_key`
- **Location**: `header`
- **Parameter Name**: `Authorization`
- **Format**: `Token token={token}`

## 2. Available Endpoints

### Incidents Endpoint
- **Path**: `/incidents`
- **HTTP Method**: `GET`
- **Required Query Parameters**: `since`, `until`
- **Optional Query Parameters**: `date_range`, `statuses[]`, `incident_key`, `service_ids[]`, `team_ids[]`, `user_ids[]`, `urgencies[]`, `time_zone`, `sort_by`, `include[]`
- **Response Data Structure**: JSON array located at `incidents`

#### Pagination Configuration
- **Type**: `offset`
- **Limit Parameter**: `limit`
- **Offset Parameter**: `offset`
- **Default Limit**: 100
- **How Next Page is Determined**: Increment `offset` by `limit` until `more` is `False` in the response.

#### Data Extraction
- **JSONPath**: `incidents`
- **Primary Key**: `id`
- **Key Fields**: `id`, `last_status_change_at`

### Services Endpoint
- **Path**: `/services`
- **HTTP Method**: `GET`
- **Optional Query Parameters**: `team_ids[]`, `time_zone`, `sort_by`, `query`, `include[]`
- **Response Data Structure**: JSON array located at `services`

#### Pagination Configuration
- **Type**: `offset`
- **Limit Parameter**: `limit`
- **Offset Parameter**: `offset`
- **Default Limit**: 100

#### Data Extraction
- **JSONPath**: `services`
- **Primary Key**: `id`

### Notifications Endpoint
- **Path**: `/notifications`
- **HTTP Method**: `GET`
- **Required Query Parameters**: `since`, `until`
- **Optional Query Parameters**: `time_zone`, `filter`, `include`
- **Response Data Structure**: JSON array located at `notifications`

#### Pagination Configuration
- **Type**: `offset`
- **Limit Parameter**: `limit`
- **Offset Parameter**: `offset`
- **Default Limit**: 100

#### Data Extraction
- **JSONPath**: `notifications`
- **Primary Key**: `id`
- **Key Fields**: `id`, `started_at`

## 3. Incremental Data Loading

### Incidents Stream
- **Incremental Support**: Yes
- **Query Parameter**: `since`
- **Date Field**: `last_status_change_at`
- **Format**: ISO 8601 (`%Y-%m-%dT%H:%M:%SZ`)
- **Recommended Initial Value**: Current date minus 180 days

### Notifications Stream
- **Incremental Support**: Yes
- **Query Parameter**: `since`
- **Date Field**: `started_at`
- **Format**: ISO 8601 (`%Y-%m-%dT%H:%M:%SZ`)
- **Recommended Initial Value**: Current date minus 90 days

## 4. Endpoint Dependencies

### Resource Relationships
- **Incidents and Log Entries**: Incidents have related log entries and alerts that can be fetched using the incident ID.
- **Mapping**: Use `incident_id` from the `incidents` endpoint to fetch related `log_entries` and `alerts`.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests**: The API enforces rate limits. If a 429 status code is received, retry after 60 seconds.

### Special Requirements
- **Headers**: 
  - `Accept`: `application/vnd.pagerduty+json;version=2`
  - `User-Agent`: `python-pagerduty-tap`
  - `Content-Type`: `application/json`
  - `From`: User's email address
- **Error Handling**: Handle 429 status codes by implementing a retry mechanism with a delay.
- **Data Type Specifications**: Ensure date fields are handled in ISO 8601 format.

This configuration guide provides the necessary parameters and instructions to set up a dlt data pipeline for the PagerDuty API, ensuring efficient data extraction and synchronization.

---
*dlt REST API Source Configuration Guide*