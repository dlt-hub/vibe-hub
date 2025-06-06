# Bigcommerce REST API Source

## dlt REST API Configuration Documentation

# dlt REST API Source Configuration Documentation for BigCommerce

This document provides a comprehensive guide to configuring a dlt data pipeline for integrating with the BigCommerce REST API. The configuration includes client setup, endpoint details, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- Base URL: `https://api.bigcommerce.com/stores/{store_hash}/v3/`
- Note: Replace `{store_hash}` with your specific store hash.

**Authentication:**
- Type: `bearer`
- Token format: `Bearer {access_token}`
- Required Headers:
  - `X-Auth-Client`: Your client ID
  - `X-Auth-Token`: Your access token

## 2. Available Endpoints

### Orders Endpoint

**Endpoint Details:**
- Path: `/orders`
- HTTP Method: `GET`
- Required Query Parameters:
  - `min_date_modified`: Start date for incremental loading
  - `sort`: `date_modified:asc`
- Response Data Structure: JSON array of order objects

**Pagination Configuration:**
- Type: `page_number`
- Parameters:
  - `page`: Page number
  - `limit`: Number of records per page (default 50)
- Next Page Determination: Increment `page` parameter until no more data is returned

**Data Extraction:**
- JSONPath: `data`
- Primary Key: `id`

### Products Endpoint

**Endpoint Details:**
- Path: `/catalog/products`
- HTTP Method: `GET`
- Required Query Parameters:
  - `date_modified:min`: Start date for incremental loading
  - `sort`: `date_modified`
  - `direction`: `asc`
- Response Data Structure: JSON array of product objects

**Pagination Configuration:**
- Type: `page_number`
- Parameters:
  - `page`: Page number
  - `limit`: Number of records per page (default 50)
- Next Page Determination: Increment `page` parameter until no more data is returned

**Data Extraction:**
- JSONPath: `data`
- Primary Key: `id`

### Customers Endpoint

**Endpoint Details:**
- Path: `/customers`
- HTTP Method: `GET`
- Required Query Parameters:
  - `min_date_modified`: Start date for incremental loading
  - `max_date_modified`: End date for incremental loading
- Response Data Structure: JSON array of customer objects

**Pagination Configuration:**
- Type: `page_number`
- Parameters:
  - `page`: Page number
  - `limit`: Number of records per page (default 50)
- Next Page Determination: Increment `page` parameter until no more data is returned

**Data Extraction:**
- JSONPath: `data`
- Primary Key: `id`

### Coupons Endpoint

**Endpoint Details:**
- Path: `/coupons`
- HTTP Method: `GET`
- Response Data Structure: JSON array of coupon objects

**Pagination Configuration:**
- Type: `page_number`
- Parameters:
  - `page`: Page number
  - `limit`: Number of records per page (default 50)
- Next Page Determination: Increment `page` parameter until no more data is returned

**Data Extraction:**
- JSONPath: `data`
- Primary Key: `id`

## 3. Incremental Data Loading

**Incremental Support:**
- Orders: `min_date_modified` parameter
- Products: `date_modified:min` parameter
- Customers: `min_date_modified` and `max_date_modified` parameters

**Date/Time Field:**
- Orders: `date_modified`
- Products: `date_modified`
- Customers: `date_modified`

**Expected Format:**
- ISO 8601 date format (e.g., `2023-10-01T00:00:00Z`)

**Recommended Initial Values:**
- Use the earliest date you want to start syncing data from.

## 4. Endpoint Dependencies

**Resource Relationships:**
- No direct dependencies between endpoints, but ensure that data is fetched in a logical order based on business needs.

## 5. API Behavior & Limits

**Rate Limiting:**
- Requests per window: Varies by plan, typically 150 requests per 30 seconds for lower plans.
- Burst limits: Adjust `limit` parameter to fit within rate limits.

**Special Requirements:**
- Custom Headers: `X-Auth-Client`, `X-Auth-Token`
- Response Format: JSON
- Error Handling: Handle HTTP 429 for rate limiting, retry after delay.

This configuration guide provides the necessary parameters and setup to effectively integrate with the BigCommerce API using a dlt data pipeline. Adjust the configuration as needed based on specific data requirements and API changes.

---
*dlt REST API Source Configuration Guide*