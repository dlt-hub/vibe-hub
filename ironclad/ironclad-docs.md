In this guide, we'll set up a complete Ironclad data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def ironclad_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.ironcladapp.com/v1/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "workflows",
            "webhooks",
            "records"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='ironclad_pipeline',
        destination='duckdb',
        dataset_name='ironclad_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(ironclad_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from ironclad’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Workflows: Manage and automate legal workflows.
- Webhooks: Configure webhooks to respond to various system events.
- Records: Handle document records and attachments.
- Users: Manage user accounts and permissions.

You can combine these endpoints to build pipelines that extract structured content from Ironclad workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@ironclad-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Ironclad support.
    ```shell
    dlt init dlthub:ironclad duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@ironclad-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Ironclad API, as specified in @ironclad-docs.yaml 
    Start with endpoints "workflows" and "webhooks" and skip incremental loading for now. 
    Place the code in ironclad_pipeline.py and name the pipeline ironclad_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python ironclad_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Uses OAuth 2.0 with refresh tokens for authentication. Requires client ID, client secret, and refresh token to obtain access tokens.
    
    To get appropriate API keys, please visit the original source at https://www.ironcladapp.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python ironclad_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline ironclad load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset ironclad_data
    The duckdb destination used duckdb:/ironclad.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline ironclad_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("ironclad_pipeline").dataset()
   # get "workflows" table as Pandas frame
   data."workflows".df().head()
    ```

## Running into errors?

OAuth scopes must be correctly set for intended API interactions. Ensure to use HTTPS due to sensitive data transmission. Webhooks have retry policies and should be handled appropriately to avoid duplicated events. Legacy tokens are deprecated and will soon be disabled. Timeouts and API rate limits must be managed with careful request handling and backoff strategies.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)