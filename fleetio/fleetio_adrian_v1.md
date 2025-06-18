# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the Fleetio REST API. It includes details on client configuration, available endpoints, incremental data loading, endpoint dependencies, and API behavior and limits.

## 1. Client Configuration

**Base URL:**
- `https://secure.fleetio.com/api`
- Ensure to include the correct API version in the endpoint paths as specified in each stream.

**Authentication:**
- **Type:** `api_key`
- **Location:** Header
- **Parameter Name:** `Authorization`
- **Format:** `Token {api_key}`
- Additional required headers:
  - `Account-Token`: `{account_token}`
  - `X-Client-Name`: `data_connector`
  - `X-Client-Platform`: `fleetio_singer_tap`
  - `X-Api-Version`: `2024-03-15`

## 2. Available Endpoints

For each REST API endpoint, the following details are provided:

### Issues
- **Path:** `/v2/issues`
- **Method:** GET
- **Data Selector:** `$.records[*]`
- **Primary Key:** `id`
- **Pagination:**
  - **Type:** `cursor`
  - **Cursor Parameter:** `start_cursor`
  - **Page Size Parameter:** `per_page`
  - **Default Page Size:** 100

### Service Entries
- **Path:** `/v2/service_entries`
- **Method:** GET
- **Data Selector:** `$.records[*]`
- **Primary Key:** `id`
- **Pagination:** Same as Issues

### Submitted Inspection Forms
- **Path:** `/v1/submitted_inspection_forms`
- **Method:** GET
- **Data Selector:** `$.records[*]`
- **Primary Key:** `id`
- **Pagination:** Same as Issues

### Vehicles
- **Path:** `/v1/vehicles`
- **Method:** GET
- **Data Selector:** `$.records[*]`
- **Primary Key:** `id`
- **Pagination:** Same as Issues

### Expense Entries
- **Path:** `/v1/expense_entries`
- **Method:** GET
- **Data Selector:** `$.records[*]`
- **Primary Key:** `id`
- **Pagination:** Same as Issues

### Contacts
- **Path:** `/v2/contacts`
- **Method:** GET
- **Data Selector:** `$.records[*]`
- **Primary Key:** `id`
- **Pagination:** Same as Issues

### Fuel Entries
- **Path:** `/v1/fuel_entries`
- **Method:** GET
- **Data Selector:** `$.records[*]`
- **Primary Key:** `id`
- **Pagination:** Same as Issues

### Parts
- **Path:** `/v1/parts`
- **Method:** GET
- **Data Selector:** `$.records[*]`
- **Primary Key:** `id`
- **Pagination:** Same as Issues

### Purchase Orders
- **Path:** `/v1/purchase_orders`
- **Method:** GET
- **Data Selector:** `$.records[*]`
- **Primary Key:** `id`
- **Pagination:** Same as Issues

### Vehicle Assignments
- **Path:** `/v1/vehicle_assignments`
- **Method:** GET
- **Data Selector:** `$.records[*]`
- **Primary Key:** `id`
- **Pagination:** Same as Issues

## 3. Incremental Data Loading

**Incremental Support:**
- **Cursor Parameter:** `start_cursor`
- **Date/Timestamp Field:** Not explicitly provided; relies on cursor-based pagination.
- **Format:** Not applicable as cursor-based pagination is used.

## 4. Endpoint Dependencies

There are no explicit dependencies between endpoints in the provided configuration. Each endpoint can be accessed independently.

## 5. API Behavior & Limits

**Rate Limiting:**
- Specific rate limits are not provided in the documentation. It is recommended to consult Fleetio's API documentation or support for rate limit details.

**Special Requirements:**
- Ensure all required headers are included in each request.
- Handle pagination using the `next_cursor` provided in API responses.
- Implement error handling to manage API response errors and retries.

This configuration guide provides the necessary parameters and structure to set up a dlt data pipeline for the Fleetio API, ensuring efficient data extraction and integration.