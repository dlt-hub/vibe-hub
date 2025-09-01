In this guide, we'll set up a complete BigMarker data pipeline from API credentials to your first data load in just 10 minutes. You'll end up with a fully declarative Python pipeline based on dlt's REST API connector.

```python-outcome
import dlt
from dlt.sources.rest_api import (
    RESTAPIConfig,
    rest_api_resources,
)

@dlt.source
def bigmarker_conferences_source(access_token=dlt.secrets.value):
    config: RESTAPIConfig = {
        "client": {
            "base_url": "https://www.bigmarker.com/api/v1/",
            "auth": {
                "type": "bearer",
                "token": access_token,
            },
        },
        "resources": [
            "conferences",
            "conferences/conference_id/upload_file",
            "conferences/conference_id/handouts"
            ],
    }

    yield from rest_api_resources(config)


def get_data() -> None:
    pipeline = dlt.pipeline(
        pipeline_name='bigmarker_conferences_pipeline',
        destination='duckdb',
        dataset_name='bigmarker_conferences_data', 
    )
    access_token = "my_access_token"
    load_info = pipeline.run(bigmarker_conferences_source(access_token))
    print(load_info)  # noqa
```

### Why use dlt for this?

- dlt is fully declarative, while being python-native and enabling imperative customization
- Offers schema evolution with type inference for resilient, low maintenance pipelines
- Performance and scalability control
- Shallow learning curve - the pipeline is easy to extend by any team member
- dlt is the tool of choice for Pythonic Iceberg Lakehouses

## What you’ll do

We’ll show you how to generate a readable and easily maintainable Python script that fetches data from bigmarker_conferences’s API and loads it into Iceberg, DataFrames, files, or a database of your choice. Here are some of the endpoints you can load:

- Conference Management: Create, read, update, and delete conferences, as well as manage associated sessions and recurring conferences.
- File Management: Handle file uploads, deletions, and updates within conference contexts.
- Handout Management: Add, update, and delete handouts linked to specific conferences.
- Attendance and Statistics: Access live attendee lists, conference statistics, and recordings.
- Access Control: Manage entry to conferences and recording publication settings.

You can combine these endpoints to build pipelines that extract structured content from BigMarker workspaces at scale — via REST APIs or webhook ingestion.

## Setup & steps to follow

```default
Before getting started, let's make sure Cursor is set up correctly:
   - Use a model like Claude 3.7 Sonnet or better
   - Add the specification file **@bigmarker_conferences-docs.yaml** as context
   - Index the REST API Source tutorial: https://dlthub.com/docs/dlt-ecosystem/verified-sources/rest_api/ and add it to context as **@dlt rest api**
   - [Read our full steps on setting up Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi#23-configuring-cursor-with-documentation)
```

Now you're ready to get started! 

1. ⚙️ **Execute these commands in a new Cursor shell.**
    
    Install dlt with duckdb support:
    ```shell
    pip install dlt
    ```

    Initialize a dlt pipeline with BigMarker support.
    ```shell
    dlt init dlthub:bigmarker_conferences duckdb
    ```

    The `init` command will setup some important files and folders, including `requirements.txt`. Install the requirements for the rest of the project.
    ```shell
    pip install -r requirements.txt
    ```
    
2. 🤠 **Start vibe-coding**
    
    Make sure to add the specification file **@bigmarker_conferences-docs.yaml** as context to the chat before prompting
    Here’s a nice prompt for you to start: 
    
    ```prompt
    Please generate a REST API Source for BigMarker API, as specified in @bigmarker_conferences-docs.yaml 
    Start with endpoints "conferences" and "conferences/conference_id/upload_file" and skip incremental loading for now. 
    Place the code in bigmarker_conferences_pipeline.py and name the pipeline bigmarker_conferences_pipeline. 
    If the file exists, use it as a starting point. 
    Do not add or modify any other files. 
    Use @dlt rest api as a tutorial. 
    After adding the endpoints, allow the user to run the pipeline with python bigmarker_conferences_pipeline.py and await further instructions.
    ```

    
3. 🔒 **Setup credentials** 
    
    Authentication is done using an API key, which must be included in the header of HTTP requests. The header name for the API key is 'API-KEY'.
    
    To get appropriate API keys, please visit the original source at https://www.bigmarker.com/.
    If you want to protect your environment secrets in a production environment, look into [setting up credentials with dlt](https://dlthub.com/docs/walkthroughs/add_credentials).
    
4. 🏃‍♀️ **Run the pipeline in the Python terminal in Cursor**
    
    ```shell
    python bigmarker_conferences_pipeline.py
    ```
    
    If your pipeline runs correctly you’ll see something like the following:
    
    ```shell
    Pipeline bigmarker_conferences load step completed in 0.26 seconds
    1 load package(s) were loaded to destination duckdb and into dataset bigmarker_conferences_data
    The duckdb destination used duckdb:/bigmarker_conferences.duckdb location to store data
    Load package 1749667187.541553 is LOADED and contains no failed jobs
    ```
    
5. 📈 **See data**
    
    ```shell
    dlt pipeline bigmarker_conferences_pipeline show --marimo
    ```
    
6. 🐍 **Get your data in Python**
    
    ```python
    import dlt

   data = dlt.pipeline("bigmarker_conferences_pipeline").dataset()
   # get "conferences" table as Pandas frame
   data."conferences".df().head()
    ```

## Running into errors?

It is crucial to handle the API key securely to avoid unauthorized access. Errors such as '401 Unauthorized' indicate issues with the API key, while '404 Not Found' errors suggest problems with the specified endpoint or resource IDs. Additionally, be aware of potential permissions issues as indicated by '401 channel_permission_denied'.

### Extra resources:

- [Learn more with our 1h vibe coding course!](https://www.youtube.com/watch?v=GGid70rnJuM)

## Next steps

- [How to deploy a pipeline](https://dlthub.com/docs/walkthroughs/deploy-a-pipeline)
- [How-to guide: Creating REST API Sources with Cursor](https://dlthub.com/docs/dlt-ecosystem/llm-tooling/cursor-restapi)