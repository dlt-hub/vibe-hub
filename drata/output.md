In this guide, we'll set up a complete JumpCloud data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def jumpcloud_source_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://console.jumpcloud.com/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "personnel",
            "events",
            "controls"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='jumpcloud_source_pipeline',
        destination='duckdb',
        dataset_name='jumpcloud_source_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(jumpcloud_source_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from jumpcloud_source’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Personnel Management: Handles data regarding personnel, such as retrieving personnel information.
- Event Management: Manages and retrieves information about various public events.
- Control Monitoring: Deals with monitoring and retrieving controls that are not actively monitored.
- Compliance and Security: Focuses on compliance policies, security logs, and incident reports to ensure system integrity and security compliance.

You can combine these endpoints to build pipelines that extract structured content from JumpCloud workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@jumpcloud_source-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with JumpCloud support.
    ```shell
    dlt init dlthub:jumpcloud_source duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@jumpcloud_source-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for JumpCloud API, as specified in @jumpcloud_source-docs.yaml 
    Start with endpoints "personnel" and "events" and skip incremental loading for now. 
    Place the code in jumpcloud_source_pipeline.py and name the pipeline jumpcloud_source_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python jumpcloud_source_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication uses an API key located in the header. The key is named 'Authorization' and is expected to be passed as a Bearer token.
    
    To get appropriate API keys, please visit the original source at https://www.jumpcloud.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python jumpcloud_source_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline jumpcloud_source load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset jumpcloud_source_data
    The duckdb destination used duckdb:/jumpcloud_source.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline jumpcloud_source_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("jumpcloud_source_pipeline").dataset()
   # get "personnel" table as Pandas frame
   data."personnel".df().head()
    ```

## Running into errors?

A rate limit of 500 requests per minute is enforced per unique source IP. Ensure that specific APIs are enabled in the project. Drata does not support nested groups, only top-level group members are synced. There are limitations on pulling members within groups. JumpCloud’s Patch Management feature is not fully supported at this time.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)