# Overview

This document provides a configuration guide for integrating with a third-party domain information API using the dltHub REST API source format. The integration allows for retrieving detailed information about domain registrations, including administrative, billing, and technical contact details, as well as registrar information and domain status.

## Configuration Example

```python
import dlt
from dlt.sources.rest_api import rest_api_source

source = rest_api_source({
    "client": {
        "base_url": "https://api.example.com",
        "auth": {
            "type": "api_key",
            "name": "api_key",
            "api_key": dlt.secrets["api_key"],
            "location": "query"
        }
    },
    "resource_defaults": {
        "primary_key": "domain_id",
        "write_disposition": "merge"
    },
    "resources": [
        {
            "name": "domain_info",
            "endpoint": {
                "path": "/v1/resource",
                "data_selector": "data",
                "paginator": {
                    "type": "single_page"
                }
            }
        }
    ]
})
```

## Authentication

- **Type**: `api_key`
- **Secrets or Config Values**: 
  - `api_key`: The API key used for authenticating requests.
  - `location`: The API key is included in the query parameters of the request.

## Resources

- **Resource Name**: `domain_info`
- **Endpoint Path and Method**: 
  - **Path**: `/v1/resource`
  - **Method**: `GET`
- **Pagination Strategy**: `single_page`
- **Data Selector**: `data`

## Error Handling

- **Retry/Backoff Logic**: Not specified in the YAML, but can be configured as needed.
- **Known HTTP Response Codes**: Not specified in the YAML, but typical codes like 429, 500, 502, 503, and 504 can be handled with retries.
- **Fallback/Handling Behavior**: Not specified in the YAML.

## Schema

The schema defines the structure of the data retrieved from the API. It includes various fields related to domain registration and contact information. Below are the key fields:

- **Domain Information**:
  - `domain`: Domain name
  - `domain_id`: Unique identifier for the domain
  - `status`: Status of the domain registration
  - `create_date`: Date and time when the domain was created
  - `update_date`: Date and time when the domain record was last updated
  - `expire_date`: Date and time when the domain will expire
  - `domain_age`: Age of the domain in years
  - `whois_server`: Whois server used to query the domain registration information

- **Contact Information**:
  - **Administrative Contact**: Details such as name, organization, street address, city, region, zip code, country, phone, fax, and email.
  - **Billing Contact**: Similar details as the administrative contact.
  - **Registrant Contact**: Similar details as the administrative contact.
  - **Technical Contact**: Similar details as the administrative contact.

- **Registrar Information**:
  - `iana_id`: IANA ID of the registrar
  - `name`: Name of the registrar
  - `url`: URL of the registrar

- **Nameservers**: List of nameservers associated with the domain.

This configuration provides a comprehensive setup for accessing domain registration data through a REST API, using dltHub's source format.