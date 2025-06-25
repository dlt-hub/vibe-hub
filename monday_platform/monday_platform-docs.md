In this guide, we'll set up a complete monday.com data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def monday_platform_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.monday.com/v2/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "boards",
            "items",
            "groups"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='monday_platform_pipeline',
        destination='duckdb',
        dataset_name='monday_platform_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(monday_platform_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from monday_platform’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Boards Management: Manage boards including creating, updating, and retrieving boards.
- Items Management: Handle items on the boards, such as adding, updating, or deleting items.
- Groups Management: Operations related to groups within boards, like creating and managing groups.
- Column Values: Modify or retrieve values of specific columns within board items.

You can combine these endpoints to build pipelines that extract structured content from monday.com workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@monday_platform-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with monday.com support.
    ```shell
    dlt init dlthub:monday_platform duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for monday.com API, as specified in @monday_platform-docs.yaml 
    Start with endpoints "boards" and "items" and skip incremental loading for now. 
    Place the code in monday_platform_pipeline.py and name the pipeline monday_platform_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python monday_platform_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication with the monday.com API can be done using API keys or OAuth tokens. The API uses bearer token authentication, and tokens are passed via the Authorization header. An OAuth flow is available for handling tokens.
    
    To get appropriate API keys, please visit the original source at https://www.monday.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python monday_platform_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline monday_platform load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset monday_platform_data
    The duckdb destination used duckdb:/monday_platform.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline monday_platform_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("monday_platform_pipeline").dataset()
   # get boards table as Pandas frame
   data.boards.df().head()
    ```

## Running into errors?

Users should be aware of rate limits and ensure not to exceed complexity budgets. The API does not support refresh tokens, so new tokens must be obtained upon expiration. Also, certain features are in beta and subject to change, and API tokens are tied to user permissions, reflecting the UI permissions.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)