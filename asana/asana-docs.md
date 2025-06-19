# How to load Asana data in Python using dlt

**Build a Asana-to-database or-dataframe pipeline in Python using dlt with automatic Cursor support.**

Your outcome will be a fully declarative python pipeline based on dlt’s REST API connector

```python
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def asana_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://app.asana.com/api/1.0",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "/-/oauth_authorize",
            "/rule_actions",
            "/audit-log-events"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='asana_pipeline',
        destination='duckdb',
        dataset_name='asana_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(asana_source(access_token))
    print(load_info)  # noqa
```

**Why use dlt for this?**

- Fully declarative while being python native and enabling imperative customisation.
- Schema evolution with type inference for resilient, low maintenance pipelines.
- Performance and scalability control
- Easy to extend by team member, shallow learning curve
- Tool of choice for Pythonic Iceberg  Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from asana’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- OAuth Authorization: Handles the OAuth flow for user authorization and token refresh.
- Rule Actions: Allows creation of automated rules within projects.
- Events: Retrieves information about various team and project events.
- Widgets: Manages and retrieves metadata for widgets associated with tasks or workspaces.

You can combine these endpoints to build pipelines that extract structured content from Asana workspaces at scale — via REST APIs or webhook ingestion.

## Steps to follow:

The steps are:

1. **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Asana support.
    ```shell
    dlt init dlthub:asana duckdb
    ```

    The `init` command will setup some important files and folders, including `requirments.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```
    Please generate REST API Source for Asana API as specified in @asana-docs.yaml 
    Start with 2 endpoints that look the most important and skip incremental loading for now. 
    Place the code in asana_pipeline.py and name the pipeline asana_pipeline. 
    If the file exists use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as tutorial. 
    After adding the endpoints allow the user to run the pipeline with python asana_pipeline.py and await further instructions.
    
    ```
    
    **Suggestions for the best results:**
    - Use model like Claude 3.7 Sonnet or better
    - **@asana-docs.yaml** - add specification file to context
    - Index REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
    - Read more here: https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation
    
3. **Setup credentials** 
    
    Authentication requires a Personal Access Token (PAT) which is used in the Authorization header. OAuth is also supported, especially for applications requiring multiple user logins.
    
    To get appropriate API keys, please visit the original source at https://developers.asana.com.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python asana_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll se something like
    
    ```shell
    Pipeline asana load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset asana_data
    The duckdb destination used duckdb:/asana.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. **See data**
    
    ```shell
    dlt pipeline asana_pipeline show --marimo
    ```
    
6. **Get your data in Python**
    
    ```python
    import dlt
    
    data = pipeline.attach("asana_pipeline").dataset()
    # get docs table as pandas
    print(data.docs.df())
    ```

## Running into errors?

Asana API uses rate limiting and requires handling of pagination for large datasets. It's crucial to manage the handling of Personal Access Tokens securely as they provide extensive access similar to the user's own permissions. Ensure to respect the structure of returned data, especially for nested or complex JSON responses.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## What’s next

- [REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
- [Deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)