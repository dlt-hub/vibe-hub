# Twitter REST API Source

## dlt REST API Configuration Documentation

# REST API Configuration for dlt

This document provides a comprehensive guide for configuring a dlt data pipeline to integrate with the Twitter API using the `tap-twitter` connector. The configuration covers client setup, available endpoints, pagination, incremental data loading, endpoint dependencies, and API behavior.

## 1. Client Configuration

### Base URL
- **Base URL**: `https://api.twitter.com/2`
- **Version**: The API uses version 2, as indicated in the base URL.

### Authentication
- **Type**: `bearer`
- **Token Format**: The bearer token is required for authentication.
- **Parameter Name**: `Authorization`
- **Location**: Header
- **Token Requirement**: The token must be obtained via the OAuth 2.0 flow as outlined in the [Twitter API documentation](https://developer.twitter.com/en/docs/authentication/oauth-2-0/application-only).

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
- **Response Data Structure**: Data is located under the `data` key in the JSON response.

### Users Endpoint
- **Path**: `/users`
- **HTTP Method**: `GET`
- **Required Query Parameters**:
  - `ids`: Comma-separated list of user IDs.
  - `user.fields`: Comma-separated list of user fields to include.
- **Response Data Structure**: Data is located under the `data` key in the JSON response.

### Pagination Configuration
- **Type**: `cursor`
- **Parameter Name**: `next_token`
- **JSONPath for Next Page Token**: `$.meta.next_token`
- **Default Page Size**: 100 (for tweets)

## 3. Incremental Data Loading

### Incremental Support
- **Tweets Endpoint**: Incremental loading is supported using the `start_time` parameter.
- **Date/Timestamp Field**: `created_at` in ISO 8601 format.
- **Recommended Initial Value**: Use the `start_date` configuration parameter to set the earliest record date to sync.

## 4. Endpoint Dependencies

### Resource Relationships
- **Tweets and Users**: The `tweets` endpoint can include user information via expansions, which requires mapping `author_id` to user data.
- **Processing Order**: Fetch user data first if needed for enriching tweet data.

## 5. API Behavior & Limits

### Rate Limiting
- **Requests**: Twitter API has specific rate limits per endpoint. Refer to the [Twitter API rate limits documentation](https://developer.twitter.com/en/docs/twitter-api/rate-limits) for details.
- **Burst Limits**: Implement backoff strategies to handle rate limit errors.

### Special Requirements
- **Custom Headers**: Include `User-Agent` if specified in the configuration.
- **Response Format**: JSON
- **Error Handling**: Implement retries with exponential backoff for transient errors.

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
        "data_selector": "data",
        "primary_key": "id",
        "pagination": {
            "type": "cursor",
            "next_token_jsonpath": "$.meta.next_token",
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
        "data_selector": "data",
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

This configuration guide provides the necessary parameters and structure to effectively integrate with the Twitter API using dlt, ensuring a robust data pipeline setup.

---
*dlt REST API Source Configuration Guide*