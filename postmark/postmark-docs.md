In this guide, we'll set up a complete Postmark data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def postmark_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://api.postmarkapp.com/",
            "auth": {
                "type": "apikey",
                "token": access_token,
                "header_name": "X-Postmark-Server-Token"
            },
        },
        "resources": [
            "bounces",
            "message-streams",
            "outbound-stats"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='postmark_pipeline',
        destination='duckdb',
        dataset_name='postmark_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(postmark_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from postmark’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Bounces: Manage and retrieve information on email bounces.
- Message Streams: Handle and configure different streams of messages for sending.
- Outbound Stats: Access statistics related to outbound emails.
- Servers: Manage and retrieve information about servers.
- Domains: Manage and get details about domains used in sending emails.
- Sender Signatures: Manage and retrieve sender signature details.

You can combine these endpoints to build pipelines that extract structured content from Postmark workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@postmark-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with Postmark support.
    ```shell
    dlt init dlthub:postmark duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for Postmark API, as specified in @postmark-docs.yaml 
    Start with endpoints "bounces" and "message-streams" and skip incremental loading for now. 
    Place the code in postmark_pipeline.py and name the pipeline postmark_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python postmark_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication is handled via an API key which must be included in the header of each request under 'X-Postmark-Server-Token'.
    
    To get appropriate API keys, please visit the original source at https://postmarkapp.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python postmark_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline postmark load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset postmark_data
    The duckdb destination used duckdb:/postmark.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline postmark_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("postmark_pipeline").dataset()
   # get bounces table as Pandas frame
   data.bounces.df().head()
    ```

## Running into errors?

Bounces are only available for 45 days after occurrence, while outbound statistics are stored permanently. Proper API key must be ensured as requests will fail with '401 Unauthorized' error if it is incorrect. Also, ensure correct endpoint formatting to avoid '404 Not Found' errors.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)