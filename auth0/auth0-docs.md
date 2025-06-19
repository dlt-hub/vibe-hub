# How to load Auth0 data in Python using dlt

**Build a Auth0-to-database or-dataframe pipeline in Python using dlt with automatic Cursor support.**

Your outcome will be a fully declarative python pipeline based on dlt’s REST API connector

```python
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def auth0_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://YOUR_DOMAIN.auth0.com",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "/oauth/token",
            "/api/v2/",
            "/users"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='auth0_pipeline',
        destination='duckdb',
        dataset_name='auth0_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(auth0_source(access_token))
    print(load_info)  # noqa
```

**Why use dlt for this?**

- Fully declarative while being python native and enabling imperative customisation.
- Schema evolution with type inference for resilient, low maintenance pipelines.
- Performance and scalability control
- Easy to extend by team member, shallow learning curve
- Tool of choice for Pythonic Iceberg  Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from auth0’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Authentication API: Handles operations related to authentication including login, token issuance, and passwordless start.
- Management API: Manages resources like users, connections, and roles within the Auth0 platform.
- Enterprise Identity Providers: Integrates with enterprise identity providers for SSO capabilities.
- User Profiles: Manages user profile information.
- SSO Integrations: Handles Single Sign-On integration setups.
- Logs: Access and manage logs for auditing and monitoring.
- Universal Login: Provides endpoints for initiating login flows.
- Multi-factor Authentication: Manages multi-factor authentication settings and verification.

You can combine these endpoints to build pipelines that extract structured content from Auth0 workspaces at scale — via REST APIs or webhook ingestion.

## Steps to follow:

The steps are:

1. **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Auth0 support.
    ```shell
    dlt init dlthub:auth0 duckdb
    ```

    The `init` command will setup some important files and folders, including `requirments.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```
    Please generate REST API Source for Auth0 API as specified in @auth0-docs.yaml 
    Start with 2 endpoints that look the most important and skip incremental loading for now. 
    Place the code in auth0_pipeline.py and name the pipeline auth0_pipeline. 
    If the file exists use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as tutorial. 
    After adding the endpoints allow the user to run the pipeline with python auth0_pipeline.py and await further instructions.
    
    ```
    
    **Suggestions for the best results:**
    - Use model like Claude 3.7 Sonnet or better
    - **@auth0-docs.yaml** - add specification file to context
    - Index REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
    - Read more here: https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation
    
3. **Setup credentials** 
    
    Auth0 uses OAuth2 for authentication. Management API requires an access token in the HTTP Authorization header for all calls.
    
    To get appropriate API keys, please visit the original source at https://auth0.com.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python auth0_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll se something like
    
    ```shell
    Pipeline auth0 load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset auth0_data
    The duckdb destination used duckdb:/auth0.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. **See data**
    
    ```shell
    dlt pipeline auth0_pipeline show --marimo
    ```
    
6. **Get your data in Python**
    
    ```python
    import dlt
    
    data = pipeline.attach("auth0_pipeline").dataset()
    # get docs table as pandas
    print(data.docs.df())
    ```

## Running into errors?

Management API v1 is deprecated as of July 13, 2020. Always ensure using the latest supported API version. Be aware of specific configurations required for cross-origin authentication, and ensure third-party cookies are enabled in the browser for proper session handling. Rate limits apply, and proper error handling must be implemented to manage these limits effectively. Use of custom domains requires additional DNS and SSL configuration.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## What’s next

- [REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
- [Deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)