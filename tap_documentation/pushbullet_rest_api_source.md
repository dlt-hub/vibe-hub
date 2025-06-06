# Pushbullet REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the Pushbullet REST API. The configuration includes details on client setup, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

**Base URL:**
- `https://api.pushbullet.com/v2/`

**Authentication:**
- Type: `api_key`
- Location: `header`
- Parameter Name: `Access-Token`
- Format: `Bearer {token}`

## 2. Available Endpoints

### Chats Endpoint

**Endpoint Details:**
- Path: `/v2/chats`
- HTTP Method: `GET`
- Required Query Parameters: None
- Response Data Structure: JSON array located at `$.chats[*]`

**Pagination Configuration:**
- Type: `cursor`
- Parameter Names: `cursor`, `limit`
- Default Page Size: 100
- Next Page Determination: Based on the presence of a `$.cursor` in the response

**Data Extraction:**
- JSONPath: `$.chats[*]`
- Primary Key: `iden`

### Devices Endpoint

**Endpoint Details:**
- Path: `/v2/devices`
- HTTP Method: `GET`
- Required Query Parameters: None
- Response Data Structure: JSON array located at `$.devices[*]`

**Pagination Configuration:**
- Type: `cursor`
- Parameter Names: `cursor`, `limit`
- Default Page Size: 100
- Next Page Determination: Based on the presence of a `$.cursor` in the response

**Data Extraction:**
- JSONPath: `$.devices[*]`
- Primary Key: `iden`

### Pushes Endpoint

**Endpoint Details:**
- Path: `/v2/pushes`
- HTTP Method: `GET`
- Required Query Parameters: `active=true`
- Response Data Structure: JSON array located at `$.pushes[*]`

**Pagination Configuration:**
- Type: `cursor`
- Parameter Names: `cursor`, `limit`
- Default Page Size: 100
- Next Page Determination: Based on the presence of a `$.cursor` in the response

**Data Extraction:**
- JSONPath: `$.pushes[*]`
- Primary Key: `iden`

### Subscriptions Endpoint

**Endpoint Details:**
- Path: `/v2/subscriptions`
- HTTP Method: `GET`
- Required Query Parameters: None
- Response Data Structure: JSON array located at `$.subscriptions[*]`

**Pagination Configuration:**
- Type: `cursor`
- Parameter Names: `cursor`, `limit`
- Default Page Size: 100
- Next Page Determination: Based on the presence of a `$.cursor` in the response

**Data Extraction:**
- JSONPath: `$.subscriptions[*]`
- Primary Key: `iden`

## 3. Incremental Data Loading

**Incremental Support:**
- Enabled via the `modified_after` query parameter
- Date/Time Field: `modified`
- Expected Format: Unix timestamp (floating point seconds)
- Recommended Initial Value: Use the `start_date` configuration parameter

## 4. Endpoint Dependencies

**Resource Relationships:**
- No explicit dependencies between endpoints are required for initial data extraction.

## 5. API Behavior & Limits

**Rate Limiting:**
- The API may impose rate limits. Use the `X-Ratelimit-Reset` header to determine wait times for retries.

**Special Requirements:**
- Custom Headers: `User-Agent` must be set to `{tap_name}/{plugin_version}`
- Error Handling: Implement backoff strategies using the `X-Ratelimit-Reset` header to manage rate limits.

This configuration guide provides the necessary parameters and considerations for integrating with the Pushbullet API using a dlt data pipeline. Ensure that all authentication and endpoint details are correctly configured to facilitate seamless data extraction.

---
*dlt REST API Source Configuration Guide*