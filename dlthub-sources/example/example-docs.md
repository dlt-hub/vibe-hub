# How to load Example data in Python using dlt

**Build a Example-to-database or-dataframe pipeline in Python using dlt with automatic Cursor support.**

Your outcome will be a fully declarative python pipeline based on dlt’s REST API connector

```python
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def example_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.example.com",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "/users/me",
            "/users/me/event_types",
            "/api/3/tags"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='example_pipeline',
        destination='duckdb',
        dataset_name='example_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(example_source(access_token))
    print(load_info)  # noqa
```

**Why use dlt for this?**

- Fully declarative while being python native and enabling imperative customisation.
- Schema evolution with type inference for resilient, low maintenance pipelines.
- Performance and scalability control
- Easy to extend by team member, shallow learning curve
- Tool of choice for Pythonic Iceberg  Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from example’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- User Management: Operations related to users, such as retrieving user information.
- Event Management: Handle events, including fetching event types associated with a user.
- Tag Management: Operations related to handling tags.
- Contact Management: Includes endpoints for managing contact details.
- Repository Management: Manage and retrieve information related to repositories.
- Custom Objects: Operations related to handling custom objects in the system.
- Webhook Management: Handle creation, update, and deletion of webhooks.
- Import and Export: Manage import and export of customer data.
- Authentication and Authorization: Endpoints for managing OAuth flows and token management.

You can combine these endpoints to build pipelines that extract structured content from Example workspaces at scale — via REST APIs or webhook ingestion.

## Steps to follow:

The steps are:

1. **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Example support.
    ```shell
    dlt init dlthub:example duckdb
    ```

    The `init` command will setup some important files and folders, including `requirments.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```
    Please generate REST API Source for Example API as specified in @example-docs.yaml 
    Start with 2 endpoints that look the most important and skip incremental loading for now. 
    Place the code in example_pipeline.py and name the pipeline example_pipeline. 
    If the file exists use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as tutorial. 
    After adding the endpoints allow the user to run the pipeline with python example_pipeline.py and await further instructions.
    
    ```
    
    **Suggestions for the best results:**
    - Use model like Claude 3.7 Sonnet or better
    - **@example-docs.yaml** - add specification file to context
    - Index REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
    - Read more here: https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation
    
3. **Setup credentials** 
    
    The API uses OAuth2 for authentication. It requires setting up an authorization base URL, client_id, client_secret, scopes, and token URL. Refresh tokens are used and can be managed through specific token refresh URLs.
    
    To get appropriate API keys, please visit the original source at https://developers.activecampaign.com.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python example_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll se something like
    
    ```shell
    Pipeline example load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset example_data
    The duckdb destination used duckdb:/example.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. **See data**
    
    ```shell
    dlt pipeline example_pipeline show --marimo
    ```
    
6. **Get your data in Python**
    
    ```python
    import dlt
    
    data = pipeline.attach("example_pipeline").dataset()
    # get docs table as pandas
    print(data.docs.df())
    ```

## Running into errors?

The API uses OAuth2 which requires careful management of client credentials and token refresh processes. Public applications must meet higher quality standards and provide sandbox credentials for testing. Rate limits apply, with a limit of 5 requests per second per account. Care should be taken when managing asynchronous API calls to handle promise responses correctly.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## What’s next

- [REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
- [Deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)