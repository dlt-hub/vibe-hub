In this guide, we'll set up a complete Auth0 data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def auth0_management_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://YOUR_DOMAIN.auth0.com/api/v2/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "users",
            "connections",
            "emails"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='auth0_management_pipeline',
        destination='duckdb',
        dataset_name='auth0_management_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(auth0_management_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from auth0_management’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Authentication API: Handles user authentication and credential management.
- Management API: Manages resources such as users, groups, and roles within Auth0.
- Enterprise Identity Providers: Integrates with enterprise identity systems like ADFS, SAML, and LDAP.
- User Profiles: Provides access to user profile data.
- SSO Integrations: Manages single sign-on configurations and integrations.
- Logs: Retrieves and manages log entries for monitoring and auditing.
- Universal Login: Manages authentication flows and login sessions.
- Multi-Factor Authentication: Handles multi-factor authentication settings and verifications.

You can combine these endpoints to build pipelines that extract structured content from Auth0 workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@auth0_management-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Auth0 support.
    ```shell
    dlt init dlthub:auth0_management duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Auth0 API, as specified in @auth0_management-docs.yaml 
    Start with endpoints "users" and "connections" and skip incremental loading for now. 
    Place the code in auth0_management_pipeline.py and name the pipeline auth0_management_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python auth0_management_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Auth0 uses OAuth2 for authentication. Tokens are provided via Auth0's authorization server and must be included in the HTTP Authorization header as a Bearer token. An Access Token is required for all API calls.
    
    To get appropriate API keys, please visit the original source at https://auth0.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python auth0_management_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline auth0_management load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset auth0_management_data
    The duckdb destination used duckdb:/auth0_management.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline auth0_management_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("auth0_management_pipeline").dataset()
   # get users table as Pandas frame
   data.users.df().head()
    ```

## Running into errors?

It's important to handle tokens securely, ensuring that they are not exposed in client-side code. Be mindful of the specific domain used for API requests as it must match the one in the token's 'iss' claim. Rate limits apply to API calls, and using features like Universal Login requires careful configuration to avoid cross-origin issues. Auth0 SDKs should be used to facilitate integration and manage authentication flows securely.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)