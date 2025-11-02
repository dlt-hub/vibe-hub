In this guide, we'll set up a complete Okta data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector, like in the partial example code below:

```python-outcome
@dlt.source
def okta_users_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://subdomain.okta.com/api/v1/org/privacy/oktaCommunication",
            "auth": {
                "type": "bearer",
                "token": access_token,
            }
        },
        "resources": [
            "optOut", "optIn"
        ],
    }
    [...]
    yield from rest_api_resources(config)


def get_data() -> None:
    # Connect to destination
    pipeline = dlt.pipeline(
        pipeline_name='okta_users_pipeline',
        destination='duckdb',
        dataset_name='okta_users_data', 
    )
    # Load the data
    load_info = pipeline.run(okta_users_source())
    print(load_info) 
```

### Why use dltHub Workspace with LLM Context to generate Python pipelines?

- Accelerate pipeline development with AI-native context
- Debug pipelines, validate schemas and data with the integrated **Pipeline Dashboard**
- Build Python notebooks for end users of your data
- **Low maintenance** thanks to Schema evolution with type inference, resilience and self documenting REST API connectors. A shallow learning curve makes the pipeline easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses, bringing mature data loading to pythonic Iceberg with or without catalogs

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from okta_users’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- **User Endpoints**: Manage user accounts and retrieve user information.
  - `/api/v1/users/{id}`: Get details of a specific user by ID.
  - `/api/v1/users`: List all users.

- **Log Endpoints**: Access logs for monitoring and auditing purposes.
  - `/api/v1/logs`: Retrieve logs for various activities.

- **API Token Endpoints**: Manage API tokens used for authentication.
  - `/api/v1/api-tokens`: List all API tokens.
  - `/api/v1/api-tokens/current`: Retrieve the current API token.
  - `/api/v1/api-tokens/{apiTokenId}`: Get details of a specific API token by ID.

- **Attack Protection Settings**: Configure settings related to attack protection.
  - `/attack-protection/api/v1/authenticator-settings`: Manage authenticator settings.
  - `/attack-protection/api/v1/user-lockout-settings`: Manage user lockout settings.

- **Behavior Endpoints**: Define and manage behaviors for security policies.
  - `/api/v1/behaviors`: List all behaviors.
  - `/api/v1/behaviors/{behaviorId}`: Get details of a specific behavior by ID.

- **Captcha Endpoints**: Manage captcha settings and instances.
  - `/api/v1/captchas`: List all captchas.
  - `/api/v1/org/captcha`: Retrieve organization-specific captcha settings.
  - `/api/v1/captchas/{captchaId}`: Get details of a specific captcha by ID.

- **Zone Endpoints**: Manage application zones for security policies.
  - `/api/v1/zones`: List all zones.
  - `/api/v1/zones/{zoneId}`: Get details of a specific zone by ID.

- **Policy Endpoints**: Manage security policies for different types.
  - `/api/v1/policies?type=OKTA_SIGN_ON`: List sign-on policies.
  - `/api/v1/policies/{policyId}`: Get details of a specific policy by ID.

- **IAM Resource Set Endpoints**: Manage resource sets for identity and access management.
  - `/api/v1/iam/resource-sets`: List all resource sets.
  - `/api/v1/iam/resource-sets/{resourceSetIdOrLabel}`: Get details of a specific resource set by ID or label.

- **OAuth2 Client Endpoints**: Manage OAuth2 clients.
  - `/oauth2/v1/clients/{clientId}`: Get details of a specific OAuth2 client by ID.

You will then debug the Okta pipeline using our Pipeline Dashboard tool to ensure it is copying the data correctly, before building a Notebook to explore your data and build reports.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - We suggest using a model like Claude 3.7 Sonnet or better
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started!

1. ⚙️ **Set up `dlt` Workspace**
    
    Install dlt with duckdb support:
    ```shell
    pip install "dlt[workspace]"
    ```

    Initialize a dlt pipeline with Okta support.
    ```shell
    dlt init dlthub:okta_users duckdb
    ```

    The `init` command will setup the necessary files and folders for the next step.
    
2. 🤠 **Start LLM-assisted coding**
    
    Here’s a prompt to get you started:
    
    ```prompt
    Please generate a REST API Source for Okta API, as specified in @okta_users-docs.yaml 
    Start with endpoints optOut and optIn and skip incremental loading for now. 
    Place the code in okta_users_pipeline.py and name the pipeline okta_users_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python okta_users_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Set up credentials** 
    
    Auth information not found.
    
    To get the appropriate API keys, please visit the original source at https://developer.okta.com/docs/reference/api/users/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python okta_users_pipeline.py
    ```
    
    If your pipeline runs correctly, you’ll see something like the following:
    
    ```shell
    Pipeline okta_users load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset okta_users_data
    The duckdb destination used duckdb:/okta_users.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **Debug your pipeline and data with the Pipeline Dashboard**

    Now that you have a running pipeline, you need to make sure it’s correct, so you do not introduce silent failures like misconfigured pagination or incremental loading errors. By launching the dlt Workspace Pipeline Dashboard, you can see various information about the pipeline to enable you to test it. Here you can see:
    - Pipeline overview: State, load metrics
    - Data’s schema: tables, columns, types, hints
    - You can query the data itself
    
    ```shell
    dlt pipeline okta_users_pipeline show dashboard
    ```
    
6. 🐍 **Build a Notebook with data explorations and reports**

    With the pipeline and data partially validated, you can continue with custom data explorations and reports. To get started, paste the snippet below into a new marimo Notebook and ask your LLM to go from there. Jupyter Notebooks and regular Python scripts are supported as well.

    
    ```python
    import dlt

   data = dlt.pipeline("okta_users_pipeline").dataset()
   # get "optOut" table as Pandas frame
   data."optOut".df().head()
    ```

### Extra resources:

- [Learn more with our 1h LLM-assisted coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How to explore your data in marimo Notebooks](https://dlthub.com/docs/general-usage/dataset-access/marimo)
- [How to query your data in Python with dataset](https://dlthub.com/docs/general-usage/dataset-access/dataset)
- [How to create REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
