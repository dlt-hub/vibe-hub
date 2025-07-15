# How to load Freshchat data in Python using dlt

**Build a Freshchat-to-database or-dataframe pipeline in Python using dlt with automatic Cursor support.**

Your outcome will be a fully declarative python pipeline based on dlt’s REST API connector

```python
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def freshchat_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.freshchat.com",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "/conversations/{conversation_id}",
            "/messages",
            "/agents"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='freshchat_pipeline',
        destination='duckdb',
        dataset_name='freshchat_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(freshchat_source(access_token))
    print(load_info)  # noqa
```

**Why use dlt for this?**

- Fully declarative while being python native and enabling imperative customisation.
- Schema evolution with type inference for resilient, low maintenance pipelines.
- Performance and scalability control
- Easy to extend by team member, shallow learning curve
- Tool of choice for Pythonic Iceberg  Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from freshchat’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Conversation Management: Manage and retrieve data about conversations, including creating, updating, and viewing conversations.
- Messaging: Send and receive messages within a conversation, support for various message types including text, images, and templates.
- Agent Management: Handle agent information, including retrieving and updating agent details.
- Account Configuration: Access and modify account settings and configurations.
- File Handling: Manage file uploads within conversations, with limitations on size and quantity per request.

You can combine these endpoints to build pipelines that extract structured content from Freshchat workspaces at scale — via REST APIs or webhook ingestion.

## Steps to follow:

The steps are:

1. **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Freshchat support.
    ```shell
    dlt init dlthub:freshchat duckdb
    ```

    The `init` command will setup some important files and folders, including `requirments.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```
    Please generate REST API Source for Freshchat API as specified in @freshchat-docs.yaml 
    Start with 2 endpoints that look the most important and skip incremental loading for now. 
    Place the code in freshchat_pipeline.py and name the pipeline freshchat_pipeline. 
    If the file exists use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as tutorial. 
    After adding the endpoints allow the user to run the pipeline with python freshchat_pipeline.py and await further instructions.
    
    ```
    
    **Suggestions for the best results:**
    - Use model like Claude 3.7 Sonnet or better
    - **@freshchat-docs.yaml** - add specification file to context
    - Index REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
    - Read more here: https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation
    
3. **Setup credentials** 
    
    Authentication is managed via OAuth 2.0 with refresh tokens. Specific scopes are required for different operations, such as 'freshchat.conversation.view', 'freshchat.roles.view', and 'freshchat.reports.view'.
    
    To get appropriate API keys, please visit the original source at https://developers.freshchat.com.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python freshchat_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll se something like
    
    ```shell
    Pipeline freshchat load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset freshchat_data
    The duckdb destination used duckdb:/freshchat.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. **See data**
    
    ```shell
    dlt pipeline freshchat_pipeline show --marimo
    ```
    
6. **Get your data in Python**
    
    ```python
    import dlt
    
    data = pipeline.attach("freshchat_pipeline").dataset()
    # get docs table as pandas
    print(data.docs.df())
    ```

## Running into errors?

Care should be taken with OAuth scopes as specific permissions are needed for different API calls. The system uses pagination extensively, and data types and limits (e.g., file sizes up to 25 MB) must be adhered to. Errors need careful handling, especially with OAuth tokens and rate limits.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## What’s next

- [REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)
- [Deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)