# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the Twitter API using the `tap-twitter` connector. The configuration includes details on client setup, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.twitter.com/2`
- **Version Path**: `/2` is the version path included in the base URL.

### Authentication
- **Type**: `bearer`
- **Token Format**: The bearer token is required for authentication.
- **Parameter Name**: `Authorization`
- **Location**: Header
- **Token Requirements**: The token must be obtained through the OAuth 2.0 application-only flow as outlined in the [Twitter API documentation](https://developer.twitter.com/en/docs/authentication/oauth-2-0/application-only).

## 2. Available Endpoints

### Tweets Endpoint
- **Path**: `/tweets/search/recent`
- **HTTP Method**: `GET`
- **Required Query Parameters**:
  - `query`: Constructed using user IDs and optional URL patterns.
  - `max_results`: Maximum number of results per request (default is 100).
  - `tweet.fields`: Comma-separated list of tweet fields to include.
  - `expansions`: Comma-separated list of expansions (e.g., `author_id`).
  - `user.fields`: Comma-separated list of user fields to include.
- **Response Data Structure**: JSON object with `data` array containing tweet records.

### Users Endpoint
- **Path**: `/users`
- **HTTP Method**: `GET`
- **Required Query Parameters**:
  - `ids`: Comma-separated list of user IDs.
  - `user.fields`: Comma-separated list of user fields to include.
- **Response Data Structure**: JSON object with `data` array containing user records.

### Pagination Configuration
- **Type**: `cursor`
- **Parameter Name**: `next_token`
- **Determination**: The next page is determined using the `next_token` found in the `meta` section of the response.
- **Default/Maximum Page Sizes**: `max_results` is set to 100.

### Data Extraction
- **JSONPath**: `$.data[*]` for both tweets and users.
- **Primary Key**: `id` for both tweets and users.
- **Key Fields**: `id` uniquely identifies each record.

## 3. Incremental Data Loading

### Incremental Support
- **Query Parameter**: `start_time` for tweets (not explicitly used in the provided code but can be configured).
- **Date/Timestamp Field**: `created_at` for tweets.
- **Expected Format**: ISO 8601 date format.
- **Recommended Initial Values**: Use the `start_date` configuration parameter to specify the earliest record date to sync.

## 4. Endpoint Dependencies

### Resource Relationships
- **Tweets and Users**: The `TweetsStream` can include user information through expansions, linking tweets to their authors.
- **Mapping Identifiers**: The `author_id` in tweets is used to link to user records.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests per Minute**: Twitter API rate limits vary by endpoint and authentication level. Refer to the [Twitter API rate limits documentation](https://developer.twitter.com/en/docs/twitter-api/rate-limits) for specifics.
- **Burst Limits**: Implement backoff strategies to handle rate limits gracefully.

### Special Requirements
- **Custom Headers**: `User-Agent` can be specified in the configuration.
- **Response Format Considerations**: JSON responses with nested structures.
- **Error Handling Patterns**: Use backoff strategies for handling transient errors and rate limits.
- **Data Type Specifications**: Ensure correct handling of date and numeric fields as per the schema.

## Output Format

```python
# REST API Configuration for dlt

BASE_CONFIG = {
    "base_url": "https://api.twitter.com/2",
    "auth": {
        "type": "bearer",
        "location": "header",
        "name": "Authorization",
        "format": "Bearer {token}"
    }
}

ENDPOINTS = [
    {
        "name": "tweets",
        "path": "/tweets/search/recent",
        "method": "GET",
        "data_selector": "$.data[*]",
        "primary_key": "id",
        "pagination": {
            "type": "cursor",
            "next_token_param": "next_token",
            "limit": 100
        },
        "incremental": {
            "cursor_path": "created_at",
            "param_name": "start_time",
            "format": "iso"
        }
    },
    {
        "name": "users",
        "path": "/users",
        "method": "GET",
        "data_selector": "$.data[*]",
        "primary_key": "id"
    }
]

DEPENDENCIES = [
    {
        "endpoint": "tweets",
        "depends_on": "users",
        "param_mapping": {"author_id": "id"}
    }
]
```

This configuration guide provides the necessary parameters and structure to effectively integrate with the Twitter API using the `tap-twitter` connector, ensuring a robust data pipeline setup.