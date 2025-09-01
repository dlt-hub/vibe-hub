In this guide, we'll set up a complete Groove data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def groovehq_support_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.groovehq.com/v1/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "agents",
            "customers",
            "webhooks"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='groovehq_support_pipeline',
        destination='duckdb',
        dataset_name='groovehq_support_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(groovehq_support_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from groovehq_support’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Knowledge Base Management: Handles operations related to knowledge bases including creation, reading, updating, deleting, publishing, and unpublishing.
- Ticketing System: Manages ticket messages, creation of tickets, and messaging within tickets.
- Customer and Agent Management: Includes endpoints for managing customer and agent information, such as retrieving and updating details by email.
- Webhooks and Settings: Involves setting up webhooks and managing settings for knowledge bases.

You can combine these endpoints to build pipelines that extract structured content from Groove workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@groovehq_support-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Groove support.
    ```shell
    dlt init dlthub:groovehq_support duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@groovehq_support-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Groove API, as specified in @groovehq_support-docs.yaml 
    Start with endpoints "agents" and "customers" and skip incremental loading for now. 
    Place the code in groovehq_support_pipeline.py and name the pipeline groovehq_support_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python groovehq_support_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication for the Groove API utilizes a Bearer token which must be included in the Authorization header of each request. The API also supports OAuth for token management, including refresh capabilities.
    
    To get appropriate API keys, please visit the original source at https://www.groovehq.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python groovehq_support_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline groovehq_support load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset groovehq_support_data
    The duckdb destination used duckdb:/groovehq_support.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline groovehq_support_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("groovehq_support_pipeline").dataset()
   # get "agents" table as Pandas frame
   data."agents".df().head()
    ```

## Running into errors?

The API requires a knowledge base ID for certain endpoints, demands a valid email for assigning customers and agents, and uses specific endpoints for tag management. It also has strict rate limits, with potential errors like REQUEST_LIMIT_EXCEEDED, and file size constraints for uploads.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)