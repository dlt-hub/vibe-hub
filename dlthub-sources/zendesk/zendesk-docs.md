# How to load Zendesk data in Python using dlt

**Build a Zendesk-to-database or-dataframe pipeline in Python using dlt with automatic Cursor support.**

Your outcome will be a fully declarative python pipeline based on dlt’s REST API connector

```python
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def zendesk_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://developer.zendesk.com/api-reference/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "/ticketing",
            "/custom_objects",
            "/conversations"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='zendesk_pipeline',
        destination='duckdb',
        dataset_name='zendesk_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(zendesk_source(access_token))
    print(load_info)  # noqa
```

**Why use dlt for this?**

- Fully declarative while being python native and enabling imperative customisation.
- Schema evolution with type inference for resilient, low maintenance pipelines.
- Performance and scalability control
- Easy to extend by team member, shallow learning curve
- Tool of choice for Pythonic Iceberg  Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from zendesk’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Ticketing: Manage and track tickets.
- Custom Objects: Create and manage custom objects for extending functionality.
- Legacy Custom Objects: Manage old custom object systems.
- Conversations: Handle messaging and customer conversations.
- Web Widgets: Integrate Zendesk functionality into web applications.
- Apps: Manage applications within Zendesk across different modules like support, chat, and sell.
- ZIS (Zendesk Integration Services): Configure and manage integrations and webhooks.

You can combine these endpoints to build pipelines that extract structured content from Zendesk workspaces at scale — via REST APIs or webhook ingestion.

## Steps to follow:

The steps are:

1. **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Zendesk support.
    ```shell
    dlt init dlthub:zendesk duckdb
    ```

    The `init` command will setup some important files and folders, including `requirments.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```
    Please generate REST API Source for Zendesk API as specified in @zendesk-docs.yaml 
    Start with 2 endpoints that look the most important and skip incremental loading for now. 
    Place the code in zendesk_pipeline.py and name the pipeline zendesk_pipeline. 
    If the file exists use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as tutorial. 
    After adding the endpoints allow the user to run the pipeline with python zendesk_pipeline.py and await further instructions.
    
    ```
    
    **Suggestions for the best results:**
    - Use model like Claude 3.7 Sonnet or better
    - **@zendesk-docs.yaml** - add specification file to context
    - Index REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
    - Read more here: https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation
    
3. **Setup credentials** 
    
    Zendesk supports authentication via API keys and OAuth. API keys are used directly in requests, while OAuth involves a token exchange process.
    
    To get appropriate API keys, please visit the original source at https://developer.zendesk.com.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python zendesk_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll se something like
    
    ```shell
    Pipeline zendesk load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset zendesk_data
    The duckdb destination used duckdb:/zendesk.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. **See data**
    
    ```shell
    dlt pipeline zendesk_pipeline show --marimo
    ```
    
6. **Get your data in Python**
    
    ```python
    import dlt
    
    data = pipeline.attach("zendesk_pipeline").dataset()
    # get docs table as pandas
    print(data.docs.df())
    ```

## Running into errors?

Be aware of endpoint compatibility issues with data types, especially when dealing with decimal values in JSON that might transition from integers to strings. Rate limits are enforced, so follow best practices to avoid hitting these limits. Ensure proper implementation of webhook security using API tokens and digital signatures.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## What’s next

- [REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
- [Deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)