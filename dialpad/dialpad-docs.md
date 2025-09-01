In this guide, we'll set up a complete Dialpad data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def dialpad_communications_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://dialpad.com/api/v2/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "stats_report",
            "user_authentication",
            "call_events"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='dialpad_communications_pipeline',
        destination='duckdb',
        dataset_name='dialpad_communications_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(dialpad_communications_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from dialpad_communications’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Statistics Reporting: Allows posting and retrieving statistical reports.
- User Authentication: Manages authentication for users.
- Call Management: Includes endpoints for initiating calls, handling call events, and managing call routers.
- IVR Management: Manages Interactive Voice Response configurations.
- Webhooks: Handles webhook configurations and event notifications.

You can combine these endpoints to build pipelines that extract structured content from Dialpad workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@dialpad_communications-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Dialpad support.
    ```shell
    dlt init dlthub:dialpad_communications duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@dialpad_communications-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Dialpad API, as specified in @dialpad_communications-docs.yaml 
    Start with endpoints "stats_report" and "user_authentication" and skip incremental loading for now. 
    Place the code in dialpad_communications_pipeline.py and name the pipeline dialpad_communications_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python dialpad_communications_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Dialpad uses OAuth2 for authentication. It supports the refresh token flow, requiring a client ID, client secret, and refresh token. The token is passed as a Bearer token in the Authorization header.
    
    To get appropriate API keys, please visit the original source at https://www.dialpad.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python dialpad_communications_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline dialpad_communications load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset dialpad_communications_data
    The duckdb destination used duckdb:/dialpad_communications.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline dialpad_communications_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("dialpad_communications_pipeline").dataset()
   # get "stats_report" table as Pandas frame
   data."stats_report".df().head()
    ```

## Running into errors?

Important considerations include handling OAuth2 refresh flows correctly, managing API rate limits, ensuring payload formats conform to specifications, and adapting to any changes as Dialpad is still in the process of standardizing some aspects. Also, certain features like JWT for webhooks need careful setup for security.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)