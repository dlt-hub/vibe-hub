# Udemy For Business REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to extract data from the Udemy for Business REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior considerations.

## 1. Client Configuration

**Base URL:**
- `https://{organization_name}.udemy.com/api-2.0/organizations/{organization_id}/`
- Replace `{organization_name}` and `{organization_id}` with your specific organization details.

**Authentication:**
- **Type:** `basic`
- **Parameters:**
  - `username`: `client_id`
  - `password`: `client_secret`
- **Location:** Header
- **Format:** Basic Authentication using `client_id` and `client_secret`.

## 2. Available Endpoints

### Courses Endpoint

**Endpoint Details:**
- **Path:** `/courses/list`
- **HTTP Method:** GET
- **Required Query Parameters:** `fields[course]` (specifies fields to retrieve)
- **Response Data Structure:** JSON array located at `$.results[*]`

**Pagination Configuration:**
- **Type:** `page_number`
- **Parameter Names:**
  - `page_size`: 100 (default)
  - `page`: Incremental page number
- **Next Page Determination:** Extract `page` from the `next` URL in the response.

**Data Extraction:**
- **JSONPath:** `$.results[*]`
- **Primary Key:** `id`

### User Activity Endpoint

**Endpoint Details:**
- **Path:** `/analytics/user-activity`
- **HTTP Method:** GET
- **Response Data Structure:** JSON array located at `$.results[*]`

**Pagination Configuration:**
- **Type:** `page_number`
- **Parameter Names:**
  - `page_size`: 1000 (default)
  - `page`: Incremental page number
- **Next Page Determination:** Extract `page` from the `next` URL in the response.

**Data Extraction:**
- **JSONPath:** `$.results[*]`
- **Primary Key:** `user_email`

### User Course Activity Endpoint

**Endpoint Details:**
- **Path:** `/analytics/user-course-activity`
- **HTTP Method:** GET
- **Response Data Structure:** JSON array located at `$.results[*]`

**Pagination Configuration:**
- **Type:** `page_number`
- **Parameter Names:**
  - `page_size`: 1000 (default)
  - `page`: Incremental page number
- **Next Page Determination:** Extract `page` from the `next` URL in the response.

**Data Extraction:**
- **JSONPath:** `$.results[*]`
- **Primary Key:** `user_email`, `course_id`

### User Progress Endpoint

**Endpoint Details:**
- **Path:** `/analytics/user-progress`
- **HTTP Method:** GET
- **Response Data Structure:** JSON array located at `$.results[*]`

**Pagination Configuration:**
- **Type:** `page_number`
- **Parameter Names:**
  - `page_size`: 1000 (default)
  - `page`: Incremental page number
- **Next Page Determination:** Extract `page` from the `next` URL in the response.

**Data Extraction:**
- **JSONPath:** `$.results[*]`
- **Primary Key:** `user_email`, `course_id`, `item_id`

## 3. Incremental Data Loading

**Incremental Support:**
- **User Progress Endpoint:**
  - **Query Parameter:** `from_date`
  - **Date Field:** `item_completion_time`
  - **Format:** `YYYY-MM-DD`
  - **Initial Value:** Use `start_date` from configuration.

## 4. Endpoint Dependencies

**Resource Relationships:**
- **User Course Activity** depends on **Courses** for `course_id`.
- **User Progress** depends on **User Course Activity** for `user_email` and `course_id`.

## 5. API Behavior & Limits

**Rate Limiting:**
- **Requests per Minute:** Not explicitly defined; monitor for 429 responses.
- **Burst Limits:** Implement exponential backoff on 429 responses.

**Special Requirements:**
- **Custom Headers:** None required beyond authentication.
- **Response Format:** JSON
- **Error Handling:** Handle 429 (rate limit) and 401 (authentication) errors gracefully.
- **Data Type Specifications:** Ensure correct handling of date and numeric fields as per schema definitions.

This configuration guide provides the necessary parameters and considerations for setting up a dlt data pipeline to efficiently extract and process data from the Udemy for Business API. Adjust configurations as needed based on specific organizational requirements and API updates.

---
*dlt REST API Source Configuration Guide*